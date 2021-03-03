### 介绍



在本文中，我们将使用 Go 语言，编写一个最小的 UNIX（-like）操作系统 SHELL，它只需要大概 60 行代码。你需要稍微了解一些 Go 语言（知道如何编译简单的项目），以及简单使用 UNIX Shell。



> UNIX 非常简单，简单到一个天才都能理解它的简单性 - Dennis Ritchie



当然，我并非天才，我也不太确定 Dennis Ritchie 所说的，是否也包括运行于用户空间的工具。Shell 只是完整操作系统的一小部分（相较于内核，它真的是一个简单的部分），但我希望在本文的结尾，你可以感到吃惊，吃惊于编写一个 SHELL，所用到的知识如此少。



### 什么是SHELL



给 SHELL 下定义有点困难。我认为 SHELL 可以理解为你所使用的操作系统，基本的用户界面。你可以在 SHELL 中输入命令，然后接收一些反馈输出。如果想了解更多信息，或者更明确的定义，请查阅 [维基百科](https://en.wikipedia.org/wiki/Shell_(computing))。



一些 SHELL 的例子：



- [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell))
- [Zsh](https://en.wikipedia.org/wiki/Z_shell)
- [Gnome Shell](https://en.wikipedia.org/wiki/GNOME_Shell)
- [Windows Shell](https://en.wikipedia.org/wiki/Windows_shell)



有像 Windows 和 GNOME 这种图形界面 SHELL，但大多数 IT 相关人员（至少我是），当谈论起 SHELL，指的是基于文本的 SHELL（上面列表的头两项）。当然，也可以简化的定义为非图形界面 SHELL。



事实上，SHELL 的功能可以定义为输入命令，然后接收该命令的输出。想看个例子？运行 `ls` 命令，输出目录的内容。





