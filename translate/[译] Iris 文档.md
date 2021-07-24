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



```
m := mvc.New(booksAPI)
m.Handle(new(BookController))
```



```
type BookController struct {
    /* dependencies */
}

// GET: http://localhost:8080/books
func (c *BookController) Get() []Book {
    return []Book{
        {"Mastering Concurrency in Go"},
        {"Go Design Patterns"},
        {"Black Hat Go"},
    }
}

// POST: http://localhost:8080/books
func (c *BookController) Post(b Book) int {
    println("Received Book: " + b.Title)

    return iris.StatusCreated
}
```



此时启动你的 Iris Web 服务：

```
$ go run main.go
> Now listening on: http://localhost:8080
> Application started. Press CTRL+C to shut down.
```



书籍列表：



```
$ curl --header 'Accept-Encoding:gzip' http://localhost:8080/books

[
  {
    "title": "Mastering Concurrency in Go"
  },
  {
    "title": "Go Design Patterns"
  },
  {
    "title": "Black Hat Go"
  }
]
```



创建书籍：

```
$ curl -i -X POST \
--header 'Content-Encoding:gzip' \
--header 'Content-Type:application/json' \
--data "{\"title\":\"Writing An Interpreter In Go\"}" \
http://localhost:8080/books

> HTTP/1.1 201 Created
```



此时的错误信息：



```
$ curl -X POST --data "{\"title\" \"not valid one\"}" \
http://localhost:8080/books

> HTTP/1.1 400 Bad Request

{
  "status": 400,
  "title": "Book creation failure"
  "detail": "invalid character '\"' after object key",
}
```



### 性能测试



Iris 使用一个自定义版的 [muxie](https://github.com/kataras/muxie)。

[查看全部测试](https://github.com/kataras/server-benchmarks)

📖 发起 200000 请求，带着动态的整数型参数，JSON 作为请求内容，然后再在响应中返回 JSON。



```
Name	Language	Reqs/sec	Latency	Throughput	Time To Complete
Iris	Go	150430	826.05us	41.25MB	1.33s
Chi	Go	146274	0.85ms	39.32MB	1.37s
Gin	Go	141664	0.88ms	38.74MB	1.41s
Echo	Go	138915	0.90ms	38.15MB	1.44s
Kestrel	C#	136935	0.91ms	39.79MB	1.47s
Martini	Go	128590	0.97ms	34.57MB	1.56s
Buffalo	Go	58954	2.12ms	16.18MB	3.40s
Koa	Javascript	50948	2.61ms	14.15MB	4.19s
Express	Javascript	38451	3.24ms	13.77MB	5.21s
```



### API 示例

你可以找到一些准备好的示例：[Iris examples repository](https://github.com/iris-contrib/examples).



#### 使用 GET，POST，PUT，PATCH，DELETE 和 OPTIONS



```
func main() {
    // Creates an iris application with default middleware:
    // Default with "debug" Logger Level.
    // Localization enabled on "./locales" directory
    // and HTML templates on "./views" or "./templates" directory.
    // It runs with the AccessLog on "./access.log",
    // Recovery (crash-free) and Request ID middleware already attached.
    app := iris.Default()

    app.Get("/someGet", getting)
    app.Post("/somePost", posting)
    app.Put("/somePut", putting)
    app.Delete("/someDelete", deleting)
    app.Patch("/somePatch", patching)
    app.Header("/someHead", head)
    app.Options("/someOptions", options)

    app.Listen(":8080")
}
```



#### 地址中带参数



```
func main() {
    app := iris.Default()

    // This handler will match /user/john but will not match /user/ or /user
    app.Get("/user/{name}", func(ctx iris.Context) {
        name := ctx.Params().Get("name")
        ctx.Writef("Hello %s", name)
    })

    // However, this one will match /user/john/ and also /user/john/send
    // If no other routers match /user/john, it will redirect to /user/john/
    app.Get("/user/{name}/{action:path}", func(ctx iris.Context) {
        name := ctx.Params().Get("name")
        action := ctx.Params().Get("action")
        message := name + " is " + action
        ctx.WriteString(message)
    })

    // For each matched request Context will hold the route definition
    app.Post("/user/{name:string}/{action:path}", func(ctx iris.Context) {
        ctx.GetCurrentRoute().Tmpl().Src == "/user/{name:string}/{action:path}" // true
    })

    app.Listen(":8080")
}
```



可以响应的参数类型：



| Param Type      | Go Type | Validation                                                   | Retrieve Helper      |
| --------------- | ------- | ------------------------------------------------------------ | -------------------- |
| `:string`       | string  | anything (single path segment)                               | `Params().Get`       |
| `:uuid`         | string  | uuidv4 or v1 (single path segment)                           | `Params().Get`       |
| `:int`          | int     | -9223372036854775808 to 9223372036854775807 (x64) or -2147483648 to 2147483647 (x32), depends on the host arch | `Params().GetInt`    |
| `:int8`         | int8    | -128 to 127                                                  | `Params().GetInt8`   |
| `:int16`        | int16   | -32768 to 32767                                              | `Params().GetInt16`  |
| `:int32`        | int32   | -2147483648 to 2147483647                                    | `Params().GetInt32`  |
| `:int64`        | int64   | -9223372036854775808 to 9223372036854775807                  | `Params().GetInt64`  |
| `:uint`         | uint    | 0 to 18446744073709551615 (x64) or 0 to 4294967295 (x32), depends on the host arch | `Params().GetUint`   |
| `:uint8`        | uint8   | 0 to 255                                                     | `Params().GetUint8`  |
| `:uint16`       | uint16  | 0 to 65535                                                   | `Params().GetUint16` |
| `:uint32`       | uint32  | 0 to 4294967295                                              | `Params().GetUint32` |
| `:uint64`       | uint64  | 0 to 18446744073709551615                                    | `Params().GetUint64` |
| `:bool`         | bool    | "1" or "t" or "T" or "TRUE" or "true" or "True" or "0" or "f" or "F" or "FALSE" or "false" or "False" | `Params().GetBool`   |
| `:alphabetical` | string  | lowercase or uppercase letters                               | `Params().Get`       |
| `:file`         | string  | lowercase or uppercase letters, numbers, underscore (_), dash (-), point (.) and no spaces or other special characters that are not valid for filenames | `Params().Get`       |
| `:path`         | string  | anything, can be separated by slashes (path segments) but should be the last part of the route path |                      |



更多示例在此查看：[_examples/routing](https://github.com/kataras/iris/tree/master/_examples/routing).