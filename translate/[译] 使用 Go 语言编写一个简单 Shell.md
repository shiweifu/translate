翻译自：https://sj14.gitlab.io/post/2018/07-01-go-unix-shell/



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



```
Input:

ls
Output:

Applications			etc
Library				home
...
```



就是这样，十分简单。让我们开始吧！



### 输入循环



要执行一个命令，我们必须接收输入。而输入来自我们人类，使用键盘进行的。



键盘是我们的标准输入设备（os.Stdin），我们可以访问并读取它。当我们按下回车键的时候，会创建新的一行。这行新的文本以 `\n` 结尾。当敲击回车键的时候，所有存储在输入区的内容将被输入。



```
reader := bufio.NewReader(os.Stdin)
input, err := reader.ReadString('\n')
```



让我们将这些代码输入进我们的 `main.go` 文件，ReadingString 方法被嵌套在 for 循环中，所以我们可以反复输入命令。当读取输入，发生错误时，我们可以将错误信息输出到标准错误处理设备（os.Stderr）。如果我们使用 `fmt.Println`，但并没有指定输出设备，这个错误信息还是会输出到标准输出设备中（os.Stdout）。这并不会改变 SHELL 的功能，但是输出到单独的设备，可以方便过滤输出，以进行下一步处理。



```
func main() {
    reader := bufio.NewReader(os.Stdin)
    for {
        // Read the keyboad input.
        input, err := reader.ReadString('\n')
        if err != nil {
            fmt.Fprintln(os.Stderr, err)
        }
    }
}
```



### 执行命令



现在，我们打算执行输入的命令。增加一个名为 `execInput` 的新的函数，他接收输入的字符串作为参数。首先，我们移除输入结尾的换行符。接下来，通过  `exec.Command(input)` 来准备执行命令，设置参数，以及捕获输出的结果和错误。最后，通过 `cmd.Run()` 来执行。



```
func execInput(input string) error {
    // Remove the newline character.
    input = strings.TrimSuffix(input, "\n")

    // Prepare the command to execute.
    cmd := exec.Command(input)

    // Set the correct output device.
    cmd.Stderr = os.Stderr
    cmd.Stdout = os.Stdout

    // Execute the command and return the error.
    return cmd.Run()
}
```



### 原型



接着，在循环语句上面，添加一个美化作用的指示器（>），在循环语句下面，添加新的 `execInput` 函数，此时，主要功能就完成了。



```
func main() {
    reader := bufio.NewReader(os.Stdin)
    for {
        fmt.Print("> ")
        // Read the keyboad input.
        input, err := reader.ReadString('\n')
        if err != nil {
            fmt.Fprintln(os.Stderr, err)
        }

        // Handle the execution of the input.
        if err = execInput(input); err != nil {
            fmt.Fprintln(os.Stderr, err)
        }
    }
}
```



是时候执行一次测试了。使用 `go run main.go` 构建并运行咱们的 SHELL。你将看到输入标识符 `>`，此时可以接受输入。举个例子，我们可以执行 `ls` 命令。



```
> ls
LICENSE
main.go
main_test.go
```



不错，可以运行！咱们的程序此时可以执行 `ls` 命令，并输出当前目录的内容。你可以像退出其他程序一样，使用 `ctrl+c`，退出它。



### 参数



让我们命令后面加个参数，如 `ls -l`。

```
> ls -l
```

执此时执行会报错：`exec: "ls -l": executable file not found in $PATH`。



这是因为我们的 SHELL 尝试执行 `ls -l`，但是并没有找到叫这个名字的程序。我们的意思是执行 `ls`，带上 `-l` 的参数。当前，我们的程序还不支持接受命令参数。要修复这个问题，需要修改 `execLine` 函数，将要执行的命令以空格拆分。

```
func execInput(input string) error {
    // Remove the newline character.
    input = strings.TrimSuffix(input, "\n")

    // Split the input to separate the command and the arguments.
    args := strings.Split(input, " ")

    // Pass the program and the arguments separately.
    cmd := exec.Command(args[0], args[1:]...)
    ...
}
```



程序的名字现在存储在 args[0] 中，程序执行的参数存储在数组其他索引中。执行 `ls -l` 现在可以得到预期的结果。



```
> ls -l
total 24
-rw-r--r--  1 simon  staff  1076 30 Jun 09:49 LICENSE
-rw-r--r--  1 simon  staff  1058 30 Jun 10:10 main.go
-rw-r--r--  1 simon  staff   897 30 Jun 09:49 main_test.go
```



### 更改目录（cd）



现在，我们已经可以带着参数执行命令了。现有的这些功能，距离达到一个最小的可用性，只差了一点点。你也许在使用我们的 Shell 时候，已经注意到了：你无法通过  `cd` 改变当前命令执行的目录。



```
> cd /
> ls
LICENSE
main.go
main_test.go
```



不，这不是我们根目录的内容。那为什么 `cd` 命令不起作用呢？要理解这点很容易：没有真正的 `cd` 程序，该功能是 `SHELL` 的内置命令。



我们必须对 `execInput` 函数再次进行修改。在 `Split` 方法后面，我们添加 `switch` 结构语句，并将 args[0] 作为它的参数。当这个命令是 `cd`，我们检查它后面是否还有参数，如果没有指定参数，我们无法改变当前目录（在大多数 SHELL 中，不指定参数，将跳转到主目录）。当 `args[1]` 中有一个后续参数时（存储路径的参数），我们使用 `os.Chdir(args[1])` 更改目录。在 `case` 块的末尾，我们返回 `execInput` 函数以停止其他处理。



因为如此简单，我们在 `cd` 块后面，再添加一个 `exit` 命令，exit 可以用来退出当前SHELL（另一个退出方法是 `CTRL+C`）。



```
// Split the input to separate the command and the arguments.
args := strings.Split(input, " ")

// Check for built-in commands.
switch args[0] {
case "cd":
    // 'cd' to home dir with empty path not yet supported.
    if len(args) < 2 {
        return  errors.New("path required")
    }
    // Change the directory and return the error.
    return os.Chdir(args[1])
case "exit":
    os.Exit(0)
}
...
```



可以看到，此时输出的内容，相较于之前的输出结果，更像是我们的根目录。



```
> cd /
> ls
Applications
Library
Network
System
...
```



至此，我们已经完成了这个简单的 SHEEL 的编写。



### 考虑改善的地方



此时，如果你觉得有些无聊，你可以尝试改进这个 SHELL。下面是一些可以改善的点：



- 修改光标所在行的显示：
  - 增加当前目录
  - 增加机器名称
  - 增加当前用户
- 通过输入 up/down 键，来翻阅输入的历史



### 结尾



至此，本文已接近尾声，我希望你读的愉快。如你所见，SHELL 背后的概念十分简单。



Go 同样是一门十分简单的编程语言，它帮助我们更快的得到想要的结果。我们无需关心内存管理。Rob Pike 和 Ken Thompson，以及 Robert Griesemer 共同创造了 Go，他们也创造了 Unix，所以，使用 Go 编写 SHELL 是个很好的选择。



我也一直在学习，如果你发现本文有哪些可改进的地方，请联系我。









