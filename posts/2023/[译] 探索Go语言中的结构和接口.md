翻译自：[Exploring structs and interfaces in Go - DEV Community](https://dev.to/bnevilleoneill/exploring-structs-and-interfaces-in-go-31ho)

## 介绍

Go 是一种类型安全、静态类型化、编译的编程语言。类型系统的类型由类型名称和类型声明表示，旨在防止出现未检查的运行时类型错误。

在 Go 中，有几种内置的标识符类型，也称为预声明类型。它们包括布尔型、字符串型、数字型（float、int、complex）和其他类型。此外，还有一些复合类型，它们是由预先声明的类型组成的。

复合类型主要使用类型文字来指定。它们包括数组、接口、结构、函数、映射类型等。在本文中，我们将重点介绍 Go 中的结构和接口类型。

## 准备

为了方便地遵循本教程，对围棋有一个基本的了解是很重要的。还建议在我们的机器上安装二进制文件。[此处](https://golang.org/doc/install)提供了相关说明。然而，为了简单起见，为了本文的目的，我们将使用[the Go Playground](https://play.golang.org/)作为示例。

## Go 语言入门

Go 是一种现代、快速的编译语言（即，它从源代码生成机器代码），具有许多令人敬畏的功能。由于支持开箱即用的并发性，它也适用于与低级计算机网络和系统编程相关的领域。

为了探索它的特点，我们需要为它的发展做准备。为此，我们可以继续  [安装 Go 二进制文件](https://golang.org/doc/install)。然后，我们导航到 Go 工作区文件夹，在那里我们可以找到 bin、pkg 和 src 目录。在早期的 Go 版本（1.13 之前的版本）中，源代码必须在 src 目录中编写，该目录包含 Go 源文件。

> 这是因为 Go 需要一种查找、安装和构建源文件的方法。

这需要我们在开发机器上设置$GOPATH 环境变量，Go 使用该变量来标识工作区根文件夹的路径。因此，为了在工作区内创建一个新目录，我们必须指定如下完整路径：

```
$ mkdir -p $GOPATH/src/github.com/firebase007
```

`$GOPATH` 可以是我们机器上的任何路径，通常是`$HOME/go`，除了我们机器上 go 安装的路径。在上面指定的路径中，我们可以在该目录中有包目录，然后还有.go 文件。

`bin` 目录包含可执行的 `Go` 二进制文件。`go` 工具链及其命令集构建二进制文件并将其安装到该目录中。该工具提供了获取、构建和安装 `Go` 包的标准方法。go 工具链的文档可以在[这里](https://golang.org/cmd/go/)找到。

> 注意：pkg 目录是 Go 存储预编译文件缓存以供后续编译的地方。关于如何使用$GOPATH 编写 Go 代码的更多详细信息，请点击[这里](https://golang.org/doc/gopath_code.html)。

## 包

为了封装、依赖性管理和可重用性，程序被分组为包。[包](https://golang.org/pkg/)是存储在同一目录中并一起编译的源文件。它们存储在模块中，其中模块是执行特定操作的一组相关 Go 包。

> 注意：Go 存储库通常只包含一个模块，该模块位于存储库的根目录下。但是，存储库也可以包含多个模块。

如今，随着 Go 模块在 1.13 及以上版本中的引入，我们将运行并编译一个简单的 Go 模块或程序，如下所示：

```
retina@alex Desktop % mkdir examplePackage // create a directory on our machine outside $GOPATH/src
retina@alex Desktop % cd examplePackage  // navigate into that directory
retina@alex examplePackage % go mod init github.com/firebase007/test  // choose a module path and create a go.mod file that declares that path
go: creating new go.mod: module github.com/firebase007/test
retina@Terra-011 examplePackage % ls
go.mod
```

假设`test`是上面模块的名称，我们可以继续创建一个包目录，然后在同一目录中创建新文件。让我们看一个简单的例子。

```
retina@alex examplePackage % mkdir test
retina@alex examplePackage % ls
go.mod  test
retina@alex examplePackage % cd test
retina@alex test % ls
retina@alex test % touch test.go
retina@alex test % ls
test.go
retina@alex test % go run test.go
Hello, Go
retina@alex test % %
```

示例代码 `test.go` 在下方：

```
package main  // specifies the package name

import "fmt"

func main() {
  fmt.Println("Hello, Go")
}
```

注意：go.mod 文件声明模块的路径，其中还包括模块内所有包的导入路径前缀。这与它在工作区或远程存储库中的位置相对应。有关使用模块组织 Go 代码的更多详细信息，请参阅  [此处](https://golang.org/doc/code.html#Organization)。

## Go 语言中的类型系统

就像其他语言中的类型系统一样，GO 的类型系统指定了一组将类型属性分配给变量，函数和标识符的规则。

Go 中的类型可分为以下类别：

`String 类型`：表示一组字符串值，这是 Go 中的一个字节片。它们在创建后是不可变的或只读的。字符串是定义的类型，因为它们附加了方法

`Boolean 类型`：由预先声明的常量 true 和 false 表示

`数组类型`：相同类型的元素的编号集合。基本上，它们是切片的构建块。数组是 Go 中的值，这意味着当它们被分配给变量或作为参数传递给函数时，它们的原始值会被复制，而不是它们的内存地址

`切片类型`：切片只是底层数组的一段，或者基本上是对底层数组的引用。[]T 是具有类型 T 的元素的切片。

`指针类型`：引用类型，表示指向给定类型变量的所有指针的集合。通常，指针类型保存另一个变量的内存地址。指针的零值为零。

## 接口和结构简介

### 结构

Go 具有包含相同或不同类型字段的结构类型。结构基本上是具有逻辑意义或构造的命名字段的集合，其中每个字段都有特定的类型。

通常，结构类型是用户定义类型的组合。它们是专门的类型，因为它们允许我们在内置类型不足的情况下定义自定义数据类型。让我们用一个例子来更好地理解这一点。

比方说，我们有一个博客文章，我们打算发表。使用结构类型来表示数据字段如下所示：

```
type blogPost struct {
  author  string    // field
  title   string    // field
  postId  int       // field
}
// Note that we can create instances of a struct types
```

在上面的结构定义中，我们添加了不同的字段值。现在，要使用文字实例化或初始化结构，我们可以执行以下操作：

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int
}

func main() {
        var b blogPost // initialize the struct type

        fmt.Println(b) // print the zero value

        b = blogPost{ //
        author: "Alex",
        title: "Understand struct and interface types",
        postId: 12345,
        }

        fmt.Println(b)

}

//output
{  0}  // zero values of the struct type is shown
{Alex Understand struct and interface types 12345}
```

这里有一个[链接](https://play.golang.org/p/k8-vj2BSMvV)到 Playground 来运行上面的代码。

我们也可以使用点`.`运算符，以便在初始化结构类型中的各个字段后访问它们。让我们通过一个例子来看看我们将如何做到这一点：

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int
}

func main() {
        var b blogPost // b is a type Alias for the BlogPost
        b.author= "Alex"
        b.title="understand structs and interface types"
        b.postId=12345

        fmt.Println(b)

        b.author = "Chinedu"  // since everything is pass by value by default in Go, we can update this field after initializing - see pointer types later

        fmt.Println("Updated Author's name is: ", b.author)
}
```

同样，这里有一个[链接](https://play.golang.org/p/gI7WiBwy0af)，可以在 Playground 上运行上面的代码片段。此外，我们可以使用短文字表示法来实例化结构类型，而不使用字段名，如下所示：

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int
}

func main() {
        b := blogPost{"Alex", "understand struct and interface type", 12345}
        fmt.Println(b)

}
```

最后，如果我们在函数中只使用一次结构类型，我们可以内联定义它们，如下所示：

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int
}

func main() {

        // inline struct init
        b := struct {
          author  string
          title   string
          postId  int
         }{
          author: "Alex",
          title:"understand struct and interface type",
          postId: 12345,
        }

        fmt.Println(b)
}
```

> 注意：我们也可以用 new 关键字初始化结构类型。在这种情况下，我们可以做到：
> 
> ```
> b := new(blogPost)
> ```



然后，我们可以使用点.运算符来设置和获取字段的值，正如我们前面所看到的。让我们看一个例子：



```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {
        b := new(blogPost)

        fmt.Println(b) // zero value

        b.author= "Alex"
        b.title= "understand interface and struct type in Go"
        b.postId= 12345

        fmt.Println(*b)   // dereference the pointer     

}

//output
&{  0}
{Alex understand interface and struct type in Go 12345}
```



> 注意：正如我们从输出中看到的那样，通过使用new关键字，我们为变量b分配存储，然后初始化结构字段的零值-在这种情况下（author=“”，title=“”，postId=0）。然后返回一个指针类型*b，其中包含内存中上述变量的地址。





这里有一个[链接](https://play.golang.org/p/-6YRoVnb-RV)到Playground来运行代码。关于新关键字的行为的更多详细信息可以在[这里](https://golang.org/ref/spec#Allocation)找到。





## 指向结构的指针



在前面的示例中，我们使用了Go的默认行为，其中所有内容都是按值传递的。对于指针，情况并非如此。让我们看一个例子：



```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {
        b := &blogPost{
                author:"Alex",
                title: "understand structs and interface types",
                postId: 12345,
                }

        fmt.Println(*b)   // dereference the pointer value 

       fmt.Println("Author's name", b.author) // in this case Go would handle the dereferencing on our behalf
}
```
