本文是学习 Rails 源码时所做的笔记，目的在于探究 Rails 的工作方式。



学习资料为网上流传广泛的 《Rebuilding Rails》一书，该书实现了一个可用的 mini rails。

[noahgibbs/rulers: The Rulers framework from the Rebuilding Rails ebook - caution, will occasionally require a "git pull -f" due to rebase (github.com)](https://github.com/noahgibbs/rulers)



## 前置准备



Rails 的使用过程中，到处都是「魔法」。这些魔法让我们的业务代码，写的更有表达力，也更优雅。魔法的实现基石，是 Ruby 对象系统，和元编程。



我们需要了解以下知识：



### 作用域门的概念

与其他语言不同，ruby 中的作用域，内层并不能访问外层定义的变量，这被成为作用域门。发生作用域门的关键字：

- def
- module
- class



```
a = 5
def func
  p a
end
```

这段代码会报错，因为使用了 def 关键字定义函数，而产生了作用域门，所以会报错。



要实现在作用域门中，访问外部的变量，需要使用 `扁平作用域` 的实现方法。原理很简单，既然使用关键字定义会产生作用域门，那不使用关键字定义，不就不会产生了吗？



- 使用 define_method 代替 def

- 使用 Class.new 代替 class 关键字
- 使用 Module 代替 module 关键字



### 模块、类和对象概念 以及 self

### 代码块、proc 和 lambda 表达式

### 对象的 instance_eval



