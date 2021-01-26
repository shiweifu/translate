# 检查 Ruby 的方法定义在哪里



原文地址：https://rubyyagi.com/where-method-defined/



我最近向 [Sinatra-ActiveRecord](https://github.com/sinatra-activerecord/sinatra-activerecord/pull/103) gem 提交了一个 合并请求，然而我无法确定 `set` 方法在哪里定义，它并不能直接在 gem 的代码中看到。



![Set method](https://rubyyagi.s3.amazonaws.com/10-method-defined/set_method.png)



我想弄清楚 "set" 方法到底定义在哪里，幸运的是 Ruby 为每个对象提供了 "[method](https://ruby-doc.org/core-2.2.2/Method.html)" 方法，来实现这一功能，它告诉你有关于一个对象方法的一些信息。



### Method 对象



你可以在任意类调用 "method" 方法，举个例子：



```
class Human
  def greet
    "Howdy human"
  end
end

matz = Human.new

puts matz.method('greet')
# #<Method: Human#greet() ex.rb:2>
```



我们可以传递方法名称，作为 `method` 方法的参数，然后它将返回指定方法信息的 `method` 对象（本例中的 'great' 方法）。



你可以调用 `call` 方法，来执行这个函数：



```
class Human
  def greet
    "Howdy human"
  end
end

matz = Human.new

puts matz.method('greet').call
# "Howdy human"
```



### 检查方法的所属类



我们可以使用 `owner` 方法，来查看所属类：

```
puts matz.method('greet').owner
# Human
```



对于 "set" 方法，我想更早的检查，

```
module ActiveRecordExtension
  def database_file=(path)
    path = File.join(root, path) if Pathname(path).relative? and root
    spec = YAML.load(ERB.new(File.read(path)).result) || {}

    set :database, spec
    puts method('set').owner
    #<Class:Sinatra::Base>
  end
end
```



可以看到，`set` 方法被定义在 Sinatra::Base 类中。现在，我可以在 Github 中查看 Sinatra gem 代码，`set` 方法是否被定义，或者使用 `source_location` 方法来检查。



### 检查一个方法是否被定义



`source_location` 将告诉我们，方法被定义的文件名和行号。



继续看先前的例子：

```
module ActiveRecordExtension
  def database_file=(path)
    path = File.join(root, path) if Pathname(path).relative? and root
    spec = YAML.load(ERB.new(File.read(path)).result) || {}

    set :database, spec
    puts method('set').source_location
    # /Users/soulchild/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/sinatra-2.1.0/lib/sinatra/base.rb , 1262
  end
end
```



`set` 方法属于 Sinatra gem 中，它将会输出 `base.rb` 所在 gem 的路径，以及行号。



如果你使用的是 Mac，你可以打开 Finder，按住 `Command + Shift + G`，粘贴路径并导航过去，然后你可以打开目标文件。



当然，`set` 方法在 Sinatra gem 中的 `base.rb` 文件的 1262 行，感谢 `source_location`！



![set source code](https://rubyyagi.s3.amazonaws.com/10-method-defined/set_source.png)



我发现 `source_location` 和 `owner` 方法在追溯某个方法定义时，非常有用，特别是项目的代码仓库比较大时。希望这对你也有用。



