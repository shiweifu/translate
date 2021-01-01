翻译自：[https://blog.usejournal.com/why-byebug-will-be-your-best-friend-when-programming-in-rails-98e06f46bdb6](https://blog.usejournal.com/why-byebug-will-be-your-best-friend-when-programming-in-rails-98e06f46bdb6)

（开头有两段寒暄的内容，不翻译了，直奔主题）

Binding.pry 是一个很好的调试工具，帮助我解决了很多头疼的问题，我对它充满了感激之情。虽然我绝不会忘记我的好伙伴 Binding.pry，但是我遇到了一个新的家伙，它是一个 Ruby framework，它更好，更强大。它的名字是 ByeBug。

ByeBug 是一个 Rails 调试工具，相对 binding.pry，它提供了更多特性。我想我是十分幸运的，可以在在我学习 Ruby on Rails 的早期就发现它。

### 安装 

Rails 5 的项目默认已经安装 Byebug了。如果你还没有安装，它也很容易安装：

```
gem "byebug"
```

### 使用

使用 Byebug 和使用 binding.pry很想。把它插入到你的代码中，然后运行程序。如果一切正常，你将会发现你的程序执行已经被中断了，执行 Rails 
 进程的终端等待着你调试。

```
def some_method
    some code
    byebug   # write it in your code and run the method or program
    some other code    
end
```

### 特性

binding.pry 只是在你代码的内部暂停你的程序，并不添加任何额外的功能。Byebug 与其不同，它允许你在终端中执行一些有用的操作。这些特性包括：

- n => 执行下一行代码。只是执行到下一行代码，遇到方法调用就跳到下一行，而不会跳进方法之中。
- s => 执行栈中下一个指令。它会跳进方法中执行。
- c => 继续执行程序，直到执行完毕或者遇到下一个断点。
- l => 输出当前代码运行到哪里的行数。
- q => 退出 ByeBug，返回到程序。

ByeBug 为你的代码提供了许多不同的玩法。当你对代码的执行原理有困惑时，现在你可以配合 ByeBug 对它们一探究竟，弄清楚每行代码做了什么，而不是猜测。（我们也是这么做的。）


### 其他特性

如果你觉得 ByeBug 没有特别打动你，下面是一些真的很棒的特性，你肯定经常用的到：

- irb  => 为你开启一个交互式的 ruby session
- save => 保存 ByeBug 的历史操作记录到指定文件中，如果不指定文件名，默认会保存到 .byebug_save 文件中。
- f => 打印当前执行上下文。它可以接受一个整数参数，输出接下来要执行的内容。
- hist => 打印当前调试环境的历史信息。

可以在终端使用 type 命令，来查看 ByeBug 的命令信息：

```
(byebug) help

  break      -- Sets breakpoints in the source code
  catch      -- Handles exception catchpoints
  condition  -- Sets conditions on breakpoints
  continue   -- Runs until program ends, hits a breakpoint or reaches a line
  debug      -- Spawns a subdebugger
  delete     -- Deletes breakpoints
  disable    -- Disables breakpoints or displays
  display    -- Evaluates expressions every time the debugger stops
  down       -- Moves to a lower frame in the stack trace
  edit       -- Edits source files
  enable     -- Enables breakpoints or displays
  finish     -- Runs the program until frame returns
  frame      -- Moves to a frame in the call stack
  help       -- Helps you using byebug
  history    -- Shows byebug's history of commands
  info       -- Shows several informations about the program being debugged
  interrupt  -- Interrupts the program
  irb        -- Starts an IRB session
  kill       -- Sends a signal to the current process
  list       -- Lists lines of source code
  method     -- Shows methods of an object, class or module
  next       -- Runs one or more lines of code
  pry        -- Starts a Pry session
  quit       -- Exits byebug
  restart    -- Restarts the debugged program
  save       -- Saves current byebug session to a file
  set        -- Modifies byebug settings
  show       -- Shows byebug settings
  source     -- Restores a previously saved byebug session
  step       -- Steps into blocks or methods one or more times
  thread     -- Commands to manipulate threads
  tracevar   -- Enables tracing of a global variable
  undisplay  -- Stops displaying all or some expressions when program stops
  untracevar -- Stops tracing a global variable
  up         -- Moves to a higher frame in the stack trace
  var        -- Shows variables and its values
  where      -- Displays the backtrace
```

如果您和我一样，认为使用调试工具，比如 binding .pry 或者 Byebug 意味着您不是一个自信的程序员，或者对程序的执行情况缺乏信心。实际上恰恰相反。调试工具允许您及早且经常的测试你的代码，可以让你检查你的逻辑是否正确。切勿撞大运编程。及早测试，常常测试，这让我压力不大。

Byebug 内置的很多其他很酷的特性，可以[查看文档](https://guides.rubyonrails.org/debugging_rails_applications.html)。在继续开发 Rails 以及调试过程中，可以将其中一些放入你的工具箱中。这些只是我发现有趣的一些关键特性。
