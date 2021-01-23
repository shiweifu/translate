# 检查 Ruby 的方法定义在哪里



I was working on a pull request for [Sinatra-ActiveRecord](https://github.com/sinatra-activerecord/sinatra-activerecord/pull/103) gem recently, and I was not sure where does the “set” method is defined, as it is not shown in the gem code.



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

