翻译自：[I am a puts debuggerer | Tenderlove Making](https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html)



## 我喜欢使用 puts 进行调试


我是一名 `puts` 调试用户。我这么说，并不是诋毁使用真正调试器进行调试的人。我认为真正的调试器是很棒的，我只是从来没有花时间去把他们学好，哪怕一个。每次，当我有一段时间没有使用它们的时候，再次尝试使用它们，我都需要重新学习该如何使用。不管怎样，我想和你分享一些关于我使用 puts 进行调试的技巧。每当我不明白某件事情如何运行，而我又想搞懂他们的时候，我就会使用这些技巧进行调试。以下分享的内容，绝对不是“最佳实践”，当您使用这些代码调试完之后，应该把这些调试代码清理干净。这适用于任何情况。注意，我说`任何情况`。全局变量，重新定义方法，增加条件，操作加载路径，猴子补丁，打印调试堆栈，等任何情况。



我视图举出真实情况下存在的例子。然而，这些例子中，许多都来自我在 Rails 中，调试安全问题的时候。所以，请复用我所展示的技术，而不是直接使用我所提供的代码。我所调试的代码，是坏的，我不想你使用他。



我尝试在每一章节，描述一个具体遇到的问题，并用这个问题作为章节名称，其中的内容，是我所使用的解决方案。



另外，我放弃使用一致的声音，这是一篇日志，我不在乎（严谨）。



## 我知道我在哪，但是不知道怎么来的



有时候，我知道在调试什么问题，并定位了问题，但是，并不知道我是怎么来的。此时，我会直接 `puts caller`，来获取栈调用。



```
  LOOKUP           = Hash.new { |h, k| h[k] = Type.new(k) unless k.blank? }
```



但我需要知道默认块是如何被调用的，所以我这样做：



```
  LOOKUP           = Hash.new { |h, k|
    puts "#" * 90
    puts caller
    puts "#" * 90
    h[k] = Type.new(k) unless k.blank?
  }
```



上面的代码，将会输出 90 个散列标签，然后是调用堆栈，接着还是 90 个散列标签。这样，如果它被调用多次，我就可以很容易区分调用堆栈。注意，我叫它们“散列标签”，是为了惹恼你。



我经常使用这段代码，所以我总结为一段 Vim 快方式：



```
" puts the caller
nnoremap <leader>wtf oputs "#" * 90<c-m>puts caller<c-m>puts "#" * 90<esc>
```



有了这个快捷方式，我可以使用 `<leader>wtf`，既可插入这段代码。



### 我只想打印一次调用堆栈



只需要使用 `exit!` 在调用输出调用堆栈之后，或者调用 `raise`。`raise` 将抛出一个异常，然后你将可以看到调用堆栈。



### 我只想在某些情况下看到调用堆栈



这是一段调试代码，你可以在你需要的时候进行调用。假设我只是想在向散列表添加内容时查看堆栈：

```
LOOKUP           = Hash.new { |h, k|
    unless k.blank?
      puts "#" * 90
      puts caller
      puts "#" * 90
      h[k] = Type.new(k)
    end
  }
```



因为我有可能在各种情况下使用这段代码，所以我可以自由地添加任何类型的奇怪条件！



### 我在调用一个方法，但我不知道它在哪里了



在调用方法但不知道该方法在哪里实现的情况下，我将使用方法方法和 `source_location` 方法。例如，我在控制器中有一个调用 `render` 的操作，我想找到这个方法：



```
def show
  render params[:id]
end
```



将代码改为下面的形式：



```
def index
  p method(:render).source_location
  render params[:id]
end
```



运行下面的语句：

```
$ curl http://localhost:3000/users/xxxx
```



会输出以下日志：



```
Processing by UsersController#show as */*
  Parameters: {"id"=>"xxxx"}
["/Users/aaron/git/rails/actionpack/lib/action_controller/metal/instrumentation.rb", 40]
Completed 500 Internal Server Error in 35ms (ActiveRecord: 0.0ms)
```



现在，我知道了 `render` 方法在 [instrumentation.rb 的第 40 行](https://github.com/rails/rails/blob/6fcc3c47eb363d0d3753ee284de2fbc68df03194/actionpack/lib/action_controller/metal/instrumentation.rb#L40)。



### 我调用了 `super` 方法，但我不知道该方法会调用哪里



这里，我看到了 `render` 调用了父方法的 `render`，但我不知道该实现在哪里。在这种情况下，我调用   `method` 类型的 `super_method` 方法。



所以，我将修改代码：



```
def index
  p method(:render).source_location
  render params[:id]
end
```

为：

```
def index
  p method(:render).super_method.source_location
  render params[:id]
end
```



再次调用请求，将会获得以下日志输出：



```
Processing by UsersController#show as */*
  Parameters: {"id"=>"xxxx"}
["/Users/aaron/git/rails/actionpack/lib/action_controller/metal/rendering.rb", 34]
Completed 500 Internal Server Error in 34ms (ActiveRecord: 0.0ms)
```



现在，我看到了 `super` 的位置被输出。该方法也调用了 super，但是我们可以重复这个操作（或者使用循环），来查找我们真正关心的方法。



### 如何查看一个方法的实现？



有时候，`method` 无法正常工作，因为我们想挖掘的对象，实现了它自己版本的 `method`。举个例子，如果我们尝查看 request 对象的 `headers` 方法，，我们写出以下代码：



```
def index
  p request.method(:headers).source_location
  @users = User.all
end
```



当我发起一个请求，我将看到错误信息：



```
ArgumentError (wrong number of arguments (given 1, expected 0)):
  app/controllers/users_controller.rb:5:in `index'
```



这是因为，`request` 对象，实现它自己名为 `method` 的方法。并非我们想要的 `headers` 的方法，我将在 `Kernel` 中找到该方法，然后重新绑定至 `request`，执行以下代码：



```
def index
  method = Kernel.instance_method(:method)
  p method.bind(request).call(:headers).source_location
  @users = User.all
end
```



再次发起请求，将得到以下输出：



```
Processing by UsersController#index as */*
["/Users/aaron/git/rails/actionpack/lib/action_dispatch/http/request.rb", 201]
```



现在，我知道 `headers` 方法的实现  [在此](https://github.com/rails/rails/blob/6fcc3c47eb363d0d3753ee284de2fbc68df03194/actionpack/lib/action_dispatch/http/request.rb#L201)。



我甚至可以找到 method 方法的实现：



```
def index
  method = Kernel.instance_method(:method)
  p method.bind(request).call(:method).source_location
  @users = User.all
end
```



### 我调用了一个方法，但我不知道它将去哪里（再一次）



有时候直接的方法并不是我真正关心的方法，所以使用方法技巧来找出我要去的地方是没有帮助的。在这种情况下，我将使用一个更大的锤子，也就是 TracePoint。我们可以重做呈现示例以获得从呈现调用的所有方法的列表。我们将看到列出的方法可能不会直接从呈现调用，而是从某个地方调用。

```
  # GET /users
  # GET /users.json
  def index
    @users = User.all
    tp = TracePoint.new(:call) do |x|
      p x
    end
    tp.enable
    render 'index'
  ensure
    tp.disable
  end
`TracePoint ` 将向所有的调用事件”开火“，回调将打印出方法名称和调用的位置。一个“调用”，即是当一个 Ruby 方法被调用（而不是 C 方法）。如果还希望查看 C 方法，请使用: c_call。这个例子将产生大量的输出。我实际上只在调用的方法数量相当少或者我甚至不知道从哪里开始查找的时候才使用这种技术。
```



#### 我知道一个异常正在被引发，但我不知道在哪里



有时会引发异常，但我不知道异常实际上是在哪里引发的。这个例子使用 Rails 3.0.0(我们已经修复了这个问题) ，但是假设您有以下代码：
`TracePoint` 这里进行分配后，将会捕获所有 `call` 事件，代码块中的内容将会执行，输出所有调用的方法名称和所在的文件。一个“call”，即使当一个 Ruby 方法被调用(而不是 C 方法)。如果你想捕获 C 方法被调用，你需要使用 `:c_call` 作为参数。这个例子，将会输出一大堆内容。我只在确认被调用的内容很少的时候，或者我并不知道要寻找什么的时候，才会使用这种方式进行排查。

### 我知道有一个异常被抛出了，但我不知道是在哪发生的

有时候，一个异常被抛出了，而此时，我并不知道是在哪里抛出的。这个例子使用了 Rails 3.0.0（我们已经修复了这个问题）。假设你写了以下代码：
```
ActiveRecord::Base.logger = Logger.new $stdout

User.connection.execute "oh no!"
```


我知道这个 SQL 不会起作用，而且会有一个异常。但是让我们看看这个异常实际上是什么样子的：

```
  SQL (0.1ms)  oh no!
SQLite3::SQLException: near "oh": syntax error: oh no!
activerecord-3.0.0/lib/active_record/connection_adapters/abstract_adapter.rb:202:in `rescue in log': SQLite3::SQLException: near "oh": syntax error: oh no! (ActiveRecord::StatementInvalid)
	from activerecord-3.0.0/lib/active_record/connection_adapters/abstract_adapter.rb:194:in `log'
	from activerecord-3.0.0/lib/active_record/connection_adapters/sqlite_adapter.rb:135:in `execute'
	from test.rb:6:in `<top (required)>'
	from railties-3.0.0/lib/rails/commands/runner.rb:48:in `eval'
	from railties-3.0.0/lib/rails/commands/runner.rb:48:in `<top (required)>'
	from railties-3.0.0/lib/rails/commands.rb:39:in `require'
	from railties-3.0.0/lib/rails/commands.rb:39:in `<top (required)>'
	from script/rails:6:in `require'
	from script/rails:6:in `<main>'
```



如果您读取了这个回溯跟踪，那么看起来这个异常就像来自团队适配器。RB 线202。但是，您很快就会注意到，这段代码正在拯救一个异常，然后再次引发。那么，真正的例外是从哪里产生的呢？为了找到它，我们可以添加一些 put 语句，或者像下面这样在 Ruby 上使用-d 标志:



```
[aaron@TC okokok (master)]$ bundle exec ruby -d script/rails runner test.rb
```



D 标志将启用警告，并打印出引发异常的每一行。是的，这确实打印了很多东西，但是如果你看看最后的输出，它看起来像这样:





```
Exception `NameError' at activesupport-3.0.0/lib/active_support/core_ext/module/remove_method.rb:3 - method `_validators' not defined in Class
Exception `SQLite3::SQLException' at sqlite3-1.3.11/lib/sqlite3/database.rb:91 - near "oh": syntax error
Exception `SQLite3::SQLException' at activesupport-3.0.0/lib/active_support/notifications/instrumenter.rb:24 - near "oh": syntax error
  SQL (0.1ms)  oh no!
SQLite3::SQLException: near "oh": syntax error: oh no!
Exception `ActiveRecord::StatementInvalid' at activerecord-3.0.0/lib/active_record/connection_adapters/abstract_adapter.rb:202 - SQLite3::SQLException: near "oh": syntax error: oh no!
Exception `ActiveRecord::StatementInvalid' at railties-3.0.0/lib/rails/commands/runner.rb:48 - SQLite3::SQLException: near "oh": syntax error: oh no!
activerecord-3.0.0/lib/active_record/connection_adapters/abstract_adapter.rb:202:in `rescue in log': SQLite3::SQLException: near "oh": syntax error: oh no! (ActiveRecord::StatementInvalid)
	from activerecord-3.0.0/lib/active_record/connection_adapters/abstract_adapter.rb:194:in `log'
	from activerecord-3.0.0/lib/active_record/connection_adapters/sqlite_adapter.rb:135:in `execute'
	from test.rb:6:in `<top (required)>'
	from railties-3.0.0/lib/rails/commands/runner.rb:48:in `eval'
	from railties-3.0.0/lib/rails/commands/runner.rb:48:in `<top (required)>'
	from railties-3.0.0/lib/rails/commands.rb:39:in `require'
	from railties-3.0.0/lib/rails/commands.rb:39:in `<top (required)>'
	from script/rails:6:in `require'
	from script/rails:6:in `<main>'
```



​	您将看到这里发生了最初的异常



```
Exception `SQLite3::SQLException' at sqlite3-1.3.11/lib/sqlite3/database.rb:91 - near "oh": syntax error
```



它在这里被重新提起:



```
Exception `SQLite3::SQLException' at sqlite3-1.3.11/lib/sqlite3/database.rb:91 - near "oh": syntax error
```



它在这里重新施加了：



```
Exception `SQLite3::SQLException' at activesupport-3.0.0/lib/active_support/notifications/instrumenter.rb:24 - near "oh": syntax error
```





异常被包装和重新引发的情况应该公开原始的回溯跟踪。这是一个 bug，但它已经被修复了，我们可以改天再讨论如何修复它。



### 我想用 -d 运行一个命令行工具



假设您想使用上面的技术使用-d 标志运行 RSpec 测试，您可以这样做:



```
$ ruby -d -S rspec
```



### 我想使用-d 标志，但是我不知道如何运行这个过程



默认的耙测试任务将在子进程中运行您的测试。这意味着运行Ruby -D -S Rake不会在运行测试的子过程中设置调试标志。在这种情况下，我使用像这样的Rubyopt环境变量：





