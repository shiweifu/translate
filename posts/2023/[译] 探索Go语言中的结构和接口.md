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

在上面的结构定义中，我们添加了不同的字段值。现在，要使用文字实例化或初始化结构，我们可以执行以下操作：

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

这里有一个  [链接](https://play.golang.org/p/k8-vj2BSMvV)到操场来运行上面的代码。

我们也可以使用点 `.` 运算符，以便在初始化结构类型中的各个字段后访问它们。让我们通过一个例子来看看我们将如何做到这一点：

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

同样，这里有一个[链接](https://play.golang.org/p/gI7WiBwy0af)，可以在操场上运行上面的代码片段。此外，我们可以使用短文字表示法来实例化结构类型，而不使用字段名，如下所示：

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

请注意，使用上面的方法，我们必须始终按照在结构类型中声明字段值的相同顺序传递字段值。此外，所有字段都必须初始化。

> 注意：正如我们从输出中看到的那样，通过使用 new 关键字，我们为变量 b 分配存储，然后初始化结构字段的零值-在这种情况下（author=“”，title=“”，postId=0）。然后返回一个指针类型\*b，其中包含内存中上述变量的地址。

这里有一个[链接](https://play.golang.org/p/-6YRoVnb-RV)到 Playground 来运行代码。关于新关键字的行为的更多详细信息可以在[这里](https://golang.org/ref/spec#Allocation)找到。

## 指向结构的指针

最后，如果我们在函数中只使用一次结构类型，我们可以内联定义它们，如下所示：

在前面的示例中，我们使用了 Go 的默认行为，其中所有内容都是按值传递的。对于指针，情况并非如此。让我们看一个例子：

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

这里有一个 [链接](https://play.golang.org/p/WSPB_KUQeTM)到 Playground 来运行代码。

在我们继续学习关于方法和接口的部分时，我们将开始了解这种方法的好处。

## 嵌套或嵌入的结构字段

早些时候，我们提到结构类型是复合类型。因此，我们还可以具有嵌套在其他结构内部的结构。例如，假设我们有一个`author`和一个`blogPost结构，以下定义：

```
type blogPost struct {
  title      string
  postId     int
  published  bool 
}

type Author struct {
  firstName, lastName, Biography string
  photoId    int
}
```

然后，我们可以在blogPost结构中嵌套Author结构，如下所示：

```
package main

import "fmt"

type blogPost struct {
  author  Author // nested struct field
  title   string
  postId  int 
  published  bool  
}

type Author struct {
  firstName, lastName, Biography string
  photoId    int
}

func main() {
        b := new(blogPost)

        fmt.Println(b)

        b.author.firstName= "Alex"
        b.author.lastName= "Nnakwue"
        b.author.Biography = "I am a lazy engineer"
        b.author.photoId = 234333
        b.published=true
        b.title= "understand interface and struct type in Go"
        b.postId= 12345

        fmt.Println(*b)        

}

// output

&{{   0}  0 false}  // again default values
{{Alex Nnakwue I am a lazy engineer 234333} understand interface and struct type in Go 12345 true}
```

这是在Playground上运行代码的 [链接](https://play.golang.org/p/aWpZcpcJvGt)。

在Go中，有一个概念，即嵌套结构类型的提升字段。在这种情况下，我们可以直接访问嵌入结构中定义的结构类型，而不必深入一些层次，例如执行`b.author.firstName`。让我们看看如何实现这一点：

```
package main

import "fmt"

type Author struct {
  firstName, lastName, Biography string
  photoId    int
}

type BlogPost struct {
  Author  // directly passing the Author struct as a field - also called an anonymous field orembedded type 
  title   string
  postId  int 
  published  bool  
}

func main() {
        b := BlogPost{
        Author: Author{"Alex", "Nnakwue", "I am a lazy engineer", 234333},
        title:"understand interface and struct type in Go",
        published:true,
        postId: 12345,
        }

        fmt.Println(b.firstName) // remember the firstName field is present on the Author struct??

        fmt.Println(b)        

}

//output
Alex
{{Alex Nnakwue I am a lazy engineer 234333} understand interface and struct type in Go 12345 true}
```

这里有一个 [link](https://play.golang.org/p/Rc-dgED1QRd) 到 Playground 来运行代码。

> 注意：Go不支持继承，而是支持组合。在接下来的部分中，我们将了解如何将这些概念应用于结构和接口类型，以及如何使用方法向它们添加行为。

## Go中的方法和函数

### 方法集合

类型T的方法集由用接收器类型T声明的所有方法组成。注意，接收器是通过方法名称前面的一个额外参数指定的。有关接收器类型的更多详细信息，请点击[这里](https://golang.org/ref/spec#Method_declarations)。

> Go中的方法是具有接收器的特殊类型的函数。

在Go中，我们可以通过定义一个具有行为的类型的方法来创建该类型。本质上，方法集是一个类型必须具有的方法列表，以便实现接口。让我们看一个例子：

```
// BlogPost struct with fields defined
type BlogPost struct {
  author  string
  title   string
  postId  int  
}

// Create a BlogPost type called (under) Technology
type Technology BlogPost
```

> 注意：我们在这里使用结构类型，因为我们在本文中关注的是结构。方法也可以在其他命名类型上定义。

```
// write a method that publishes a blogPost - accepts the Technology type as a pointer receiver
func (t *Technology) Publish() {
    fmt.Printf("The title on %s has been published by %s, with postId %d\n" , t.title, t.author, t.postId)
}


// Create an instance of the type
t := Technology{"Alex","understand structs and interface types",12345}


// Publish the BlogPost -- This method can only be called on the Technology type
t.Publish()

// output
The title on understand structs and interface types has been published by Alex, with postId 12345
```

这里有一个[链接](https://play.golang.org/p/D3mVOBux2PM)到Playground来运行代码。

注意：带有指针接收器的方法将同时处理指针或值。然而，情况并非如此。关于方法集的更多详细信息可以在这里的语言规范中[找到](https://golang.org/ref/spec#Method_sets)。

## 接口

在Go中，接口主要用于封装，并允许我们编写更干净、更健壮的代码。这样做只会暴露程序中的方法和行为。正如我们在上一节中提到的，方法集将行为添加到一个或多个类型中。

接口类型定义一个或多个方法集。因此，一个类型被称为通过实现其方法来实现接口。有鉴于此，接口使我们能够组成具有共同行为的自定义类型。

在Go中，接口是隐式声明的。这意味着，如果属于接口类型的方法集的每个方法都是由一个类型实现的，则称该类型实现接口。要声明接口，请执行以下操作：

```
type Publisher interface {
    publish()  error
}
```

在上面设置的 `public()` 接口方法中，如果一个类型（例如，struct）实现了该方法，那么我们就可以说该类型实现了该接口。让我们在下面定义一种接受结构类型 `blogpost` 的方法：

```
func (b blogPost) publish() error {
   fmt.Println("The title has been published by ", b.author)
   return nil
}
```

现在来实现这个接口：

```
package main

import "fmt"

// interface definition
type Publisher interface {
     Publish()  error
}

type blogPost struct {
  author  string
  title   string
  postId  int  
}

// method with a value receiver
func (b blogPost) Publish() error {
   fmt. Printf("The title on %s has been published by %s, with postId %d\n" , b.title, b.author, b.postId)
   return nil
}

 func test(){

  b := blogPost{"Alex","understanding structs and interface types",12345}

  fmt.Println(b.Publish())

   d := &b   // pointer receiver for the struct type

   b.author = "Chinedu"


   fmt.Println(d.Publish())

}


func main() {

        var p Publisher

        fmt.Println(p)

        p = blogPost{"Alex","understanding structs and interface types",12345}

        fmt.Println(p.Publish())

        test()  // call the test function 

}

//output
<nil>
The title on understanding structs and interface types has been published by Alex, with postId 12345
<nil>
The title on understanding structs and interface types has been published by Alex, with postId 12345
<nil>
The title on understanding structs and interface types has been published by Chinedu, with postId 12345
<nil>
```

这里有一个 [链接](https://play.golang.org/p/L4SzJiaJ99w) 到 Playground 来运行代码。

我们还可以这样别名接口类型：

```
type publishPost Publisher // alias to the interface defined above - only suited for third-party interfaces
```

> 注意：如果多个类型实现相同的方法，则方法集可以形成一个接口类型。这允许我们将该接口类型作为参数传递给打算实现该接口行为的函数。通过这种方式，可以实现[多态性](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))。

与函数不同的是，方法只能从定义它们的类型的实例中调用。其好处是，与其指定我们要接受的特定数据类型作为函数的参数，不如指定需要作为参数传递给该函数的对象的行为。

让我们看看如何使用接口类型作为函数的参数。首先，让我们向结构类型添加一个方法：

```
package main

import "fmt"


type Publisher interface {
     Publish()  error
}

type blogPost struct {
  author  string
  title   string
  postId  int  
}


func (b blogPost) Publish() error {
   fmt.Printf("The title on %s has been published by %s\n" , b.title, b.author)
   return nil
}

// Receives any type that satisfies the Publisher interface
func PublishPost(publish Publisher) error {
    return publish.Publish()
}

func main() {

        var p Publisher

        fmt.Println(p)

        b := blogPost{"Alex","understand structs and interface types",12345}

        fmt.Println(b)

        PublishPost(b)

}

//output
<nil>
{Alex understand structs and interface types 12345}
The title on understand structs and interface types has been published by Alex
```

这是在 Playground 上运行代码的 [链接](https://play.golang.org/p/tTqgdV--7KX)。

正如我们前面提到的，我们可以通过值或指针类型传递方法接收器。当我们传递值时，我们会存储正在传递的值的副本。这意味着，当我们调用该方法时，我们不会对基本值进行更改。然而，当我们通过指针传递时，我们直接共享底层内存地址，从而共享底层类型中声明的变量的位置。



> 注意：作为提醒，当一个类型定义接口类型上可用的方法集时，它被称为实现接口。



同样，类型不需要指定它们实现一个接口；相反，任何类型都实现一个接口，前提是它具有签名与接口声明匹配的方法。



最后，我们将研究在Go中嵌入接口类型的签名。让我们使用一个伪示例：



```
//embedding interfaces
type interface1 interface {
    Method1()
}

type interface2 interface {
    Method2()
}

type embeddedinterface interface {
    interface1
    interface2
}

func (s structName)  method1 (){

}

func (s structName)  method2 (){

}


type structName struct {
  field1  type1
  field2  type2

}

// initialize struct type inside main func
var e embeddedinterface = structName // struct initialized
e.method1() // call method defined on struct type
```



> 注意：作为经验法则，当我们开始在软件包中具有多种类型时实现相同的方法签名时，我们可以开始重构代码并使用接口类型。这样做避免了早期的抽象。



## 关于结构类型需要注意的事项



- 字段名可以使用变量隐式指定，也可以作为没有字段名的嵌入类型指定。在这种情况下，必须将字段指定为类型名称T或指定为指向非接口类型名称*T的指针

- 字段名称必须在结构类型中是唯一的
