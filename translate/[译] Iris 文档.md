翻译自：[Iris Docs (iris-go.com)](https://www.iris-go.com/docs/#/)



网上能搜到好几个版本的 Iris 中文文档，但感觉有的很生硬，有的像机翻，我还是自己翻译一遍。



### 安装



Iris 是一个跨平台软件。



她唯一的依赖是  [Go 语言](https://golang.org/dl/)，版本大于1.14。



```
$ mkdir myapp
$ cd myapp
$ go mod init myapp
$ go get github.com/kataras/iris/v12@master
```



在你的代码中引用：



```
import "github.com/kataras/iris/v12"
```



### 可能遇到的问题



如果你在安装 Iris 时，遇到网络问题，可以通过设置  [GOPROXY 环境变量](https://github.com/golang/go/wiki/Modules#are-there-always-on-module-repositories-and-enterprise-proxies) 来尝试解决。

```
go env -w GOPROXY=https://goproxy.cn,https://gocenter.io,https://goproxy.io,direct
```



如果还没有正常工作，尝试清理你的缓存：



```
go clean --modcache
```



### 快速开始



```
# assume the following codes in main.go file
$ cat main.go
```

```
package main

import "github.com/kataras/iris/v12"

func main() {
    app := iris.New()

    booksAPI := app.Party("/books")
    {
        booksAPI.Use(iris.Compression)

        // GET: http://localhost:8080/books
        booksAPI.Get("/", list)
        // POST: http://localhost:8080/books
        booksAPI.Post("/", create)
    }

    app.Listen(":8080")
}

// Book example.
type Book struct {
    Title string `json:"title"`
}

func list(ctx iris.Context) {
    books := []Book{
        {"Mastering Concurrency in Go"},
        {"Go Design Patterns"},
        {"Black Hat Go"},
    }

    ctx.JSON(books)
    // TIP: negotiate the response between server's prioritizes
    // and client's requirements, instead of ctx.JSON:
    // ctx.Negotiation().JSON().MsgPack().Protobuf()
    // ctx.Negotiate(books)
}

func create(ctx iris.Context) {
    var b Book
    err := ctx.ReadJSON(&b)
    // TIP: use ctx.ReadBody(&b) to bind
    // any type of incoming data instead.
    if err != nil {
        ctx.StopWithProblem(iris.StatusBadRequest, iris.NewProblem().
            Title("Book creation failure").DetailErr(err))
        // TIP: use ctx.StopWithError(code, err) when only
        // plain text responses are expected on errors.
        return
    }

    println("Received Book: " + b.Title)

    ctx.StatusCode(iris.StatusCreated)
}
Copy to clipboard
```



你也可以使用 MVC 的形式来实现相同功能：



```
import "github.com/kataras/iris/v12/mvc"
```



