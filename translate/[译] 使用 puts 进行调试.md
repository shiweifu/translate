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