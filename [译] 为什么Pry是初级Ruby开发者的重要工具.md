```
puts "This worked!"
```



在终端执行这段代码，并不会输出什么。你也许已经听过一些开发者使用给力的调试工具，但你并不敢贸然尝试他们。因此，只能使用这种意大利面条式的调试方式：在编辑器中编写打印语句，看看会输出什么。这种方式并不是解决方案。



调试属于编程生涯绕不过去的那种玩意儿。不同级别的开发者都需要为代码调试bug，这项技能越早学习，越有好处。



那么，使用什么工具呢？选择有很多，不过今天我们介绍几乎所有 Ruby 开发者都熟知的一个工具：`pry`。我们将学习如何使用它，以及为什么它重要。



### 什么是 Pry？



Pry 停住了你的程序，因此可以深入的了解代码的执行，以及找出正在发生事情和需要解决的问题。Pry 乍一看让人感到困惑，但是对于初级的 Ruby 开发者，是一个离不开的工具。



#### Pry 像 IRB

很有可能，你习惯于使用 IRB（Interactive Ruby Shell），你可以在上面自由的运行 Ruby 代码，并得到反馈。在调试代码时，他也是用的上的工具。Pry 的部分工作，与 IRB 类似，但是其有更多特性。他们同样使用 REPL 的方式执行：读取，计算，输出，并重复。但 Pry 给予你更多的调试功能。例如，Pry 提供代码的着色，这在终端里编写并执行代码，很有帮助。也更容易发现错误并修复。



### 演示代码

![Simple Pry Example](.\static\images\pry_example.png)



#### 关于调试



刚刚已经说过了，这里再说一遍，调试是所有程序员整个生涯中都要做的事。并不是初学者才需要学习这些工具！因此，您越早学会他们，就能称为一名更好的程序员。



#### 通过 Pry 设置断点



是时候展示 Pry 的一个非常给力的特性了：在代码中设置断点。假设你让代码按预期的方式执行很难（我们都遇到过那种情况，对吧？）



此时，您可以在无法工作的代码部分设置一个断点，然后代码执行至此会被冻结。最好通过例子来说明一下。



假设我们检查用户的某些设置是否正确。首先我们在代码中添加 `binding.pry `。在可能引起问题的代码上方，增加 `binding.pry` ，然后重新执行代码。



![Setting our Breakpoint](.\static\images\set_binding_pry.png)



接着，我们尝试触发我们的调试代码。假设我们在本地运行 `rails server`，我们尝试访问 `localhost:3000/users/1`。



此时，会进入一个类似 `IRB` 的终端，在此，你可以测试你的代码，以及观察输出。



![Hitting Pry](.\static\images\hitting_pry.png)



此时，我们检查 `@user` 是否包含正确的用户 ID。



![Checking @user](https://www.honeybadger.io/images/blog/posts/debugging-ruby-with-pry/finding_user.png?1602785016)



同样也可以检查用户的登录状态：



![Checking logged in status](https://www.honeybadger.io/images/blog/posts/debugging-ruby-with-pry/check_logged_in.png?1602785016)









