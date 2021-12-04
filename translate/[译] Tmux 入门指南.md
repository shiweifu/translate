翻译自：[Gentle Guide to Get Started With tmux | Pragmatic Pineapple 🍍](https://pragmaticpineapple.com/gentle-guide-to-get-started-with-tmux/)



你访问到这里，很可能是因为你想提高一下你的命令行操作技能水平。恰好，这也是我编写此文的目的。从我开始学习编程开始，我一直是终端的用户，这也很棒。当我登录 shell，我的感觉像是在家里。今天，我们的终端体验更好了。我们将通过使用 tmux 这个伟大工具，来提升我们的终端水平。



### 什么是  tmux



tmux 是一个终端复用工具，就像终端的桌面管理器。它使得我们可以在一个终端窗口中，打开多个会话。它使得其他程序可以从中运行，允许您可以操纵他们。这个功能，是大多数人日常使用的 tmux 功能之一。



但是，除了作为窗口管理器之外，tmux 还可以执行以下操作：保护在远程服务器上，使用 tmux 运行的程序。当你连接上服务器之后，你去和咖啡、吃午餐了，当你回来的时候，会话被冻结或者失去响应了。你想要允许用户使用不同计算机，来访问正在远程服务器上，运行的程序。



今天，我们将关注 tmux 作为窗口管理器的方面。在未来的日志中，我们将覆盖一些 tmux 使用的高级特性，以及这些特性如何使您受益。



### 安装



你可以在所有平台上，使用包管理器安装 tmux，但让我们来介绍一下最著名的平台。在 Debian 或者 Ubuntu 上，您可以执行以下操作来安装：



```
apt install tmux
```



在 macOS 平台，你可以使用 homebrew 来安装：

```bash
brew install tmux
```

检查是否安装成功，使用下面语句：

```bash
man tmux
```

如果你看到以下输出：

![tmux Manual](https://pragmaticpineapple.com/static/ae6a05c1f9c95c209fb3832546f20767/fcda8/tmux-manual.png)tmux Manual



标识你已经安装成功。



### 开始使用 tmux

我们通过在终端中，使用 tmux 命令来启动 tmux，以查看它的全部内容。启动之后，您可以看到终端中的内容都保持不变，只是底部多了一条绿线。我们将 tmux 服务端作为客户端连接上去。tmux 在背景中的特定 PID 上运行一个服务端，当我们键入 tmux 时，我们会自动运行服务器，并连接。



因此，我们从一个名为0的会话连接到tmux服务器，正如您在屏幕的[0]部分中所看到的那样。因此tmux充当了某种标准终端会话的中转。让我们来看看当您进入一个tmux会话时会得到什么

## 



![start of a tmux session](https://pragmaticpineapple.com/static/e4c73d188da821d90805be205502967a/fcda8/tmux-start.png)



在左下角，您可以看到[0]，它表示会话。紧挨着它的是0:zsh，显示哪个窗口正在打开，以及哪个程序正在那里运行。我们刚刚开始这个会话，所以我们只打开了一个窗口，并且zsh正在那里运行。



你可以继续使用终端，就像往常一样，但那样会很无聊，对吧?让我们学习一两件在tmux初学者可以做的事情。我想让我们经历在tmux中创建和移动窗格的过程。



### Pane Management



如果您在那里使用过iTerm2并使用了窗格分割，那么您会非常喜欢这个特性。在tmux中基本相同，唯一的区别是用于创建新窗格的快捷方式。在iTerm2中，可以执行cmd + d和cmd + shift + d来垂直和水平拆分窗格。

