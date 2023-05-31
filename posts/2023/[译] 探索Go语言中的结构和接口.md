翻译自：[Exploring structs and interfaces in Go - DEV Community](https://dev.to/bnevilleoneill/exploring-structs-and-interfaces-in-go-31ho)

## 介绍

Go是一种类型安全、静态类型化、编译的编程语言。类型系统的类型由类型名称和类型声明表示，旨在防止出现未检查的运行时类型错误。

在Go中，有几种内置的标识符类型，也称为预声明类型。它们包括布尔型、字符串型、数字型（float、int、complex）和其他类型。此外，还有一些复合类型，它们是由预先声明的类型组成的。

复合类型主要使用类型文字来指定。它们包括数组、接口、结构、函数、映射类型等。在本文中，我们将重点介绍Go中的结构和接口类型。

## 准备

为了方便地遵循本教程，对围棋有一个基本的了解是很重要的。还建议在我们的机器上安装二进制文件。[此处](https://golang.org/doc/install)提供了相关说明。然而，为了简单起见，为了本文的目的，我们将使用[the Go Playground](https://play.golang.org/)作为示例。

## Go 语言入门

Go是一种现代、快速的编译语言（即，它从源代码生成机器代码），具有许多令人敬畏的功能。由于支持开箱即用的并发性，它也适用于与低级计算机网络和系统编程相关的领域。



为了探索它的特点，我们需要为它的发展做准备。为此，我们可以继续 [安装 Go 二进制文件](https://golang.org/doc/install)。然后，我们导航到Go工作区文件夹，在那里我们可以找到bin、pkg和src目录。在早期的Go版本（1.13之前的版本）中，源代码必须在src目录中编写，该目录包含Go源文件。



>  这是因为Go需要一种查找、安装和构建源文件的方法。



这需要我们在开发机器上设置$GOPATH环境变量，Go使用该变量来标识工作区根文件夹的路径。因此，为了在工作区内创建一个新目录，我们必须指定如下完整路径：



```
$ mkdir -p $GOPATH/src/github.com/firebase007
```



`$GOPATH` 可以是我们机器上的任何路径，通常是`$HOME/go`，除了我们机器上go安装的路径。在上面指定的路径中，我们可以在该目录中有包目录，然后还有.go文件。



`bin` 目录包含可执行的 `Go` 二进制文件。`go` 工具链及其命令集构建二进制文件并将其安装到该目录中。该工具提供了获取、构建和安装 `Go` 包的标准方法。go工具链的文档可以在[这里](https://golang.org/cmd/go/)找到。



> 注意：pkg目录是Go存储预编译文件缓存以供后续编译的地方。关于如何使用$GOPATH编写Go代码的更多详细信息，请点击[这里](https://golang.org/doc/gopath_code.html)。



## 包



为了封装、依赖性管理和可重用性，程序被分组为包。[包](https://golang.org/pkg/)是存储在同一目录中并一起编译的源文件。它们存储在模块中，其中模块是执行特定操作的一组相关Go包。



> 注意：Go存储库通常只包含一个模块，该模块位于存储库的根目录下。但是，存储库也可以包含多个模块。



如今，随着Go模块在1.13及以上版本中的引入，我们将运行并编译一个简单的Go模块或程序，如下所示：



```
retina@alex Desktop % mkdir examplePackage // create a directory on our machine outside $GOPATH/src
retina@alex Desktop % cd examplePackage  // navigate into that directory
retina@alex examplePackage % go mod init github.com/firebase007/test  // choose a module path and create a go.mod file that declares that path
go: creating new go.mod: module github.com/firebase007/test
retina@Terra-011 examplePackage % ls
go.mod
```



假设`test`是上面模块的名称，我们可以继续创建一个包目录，然后在同一目录中创建新文件。让我们看一个简单的例子。




