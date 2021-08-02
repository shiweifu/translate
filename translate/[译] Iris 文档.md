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



更多示例在此查看：[_examples/routing](https://github.com/kataras/iris/tree/master/_examples/routing)。



#### 查询参数



```
func main() {
    app := iris.Default()

    // Query string parameters are parsed using the existing underlying request object.
    // The request responds to a url matching:  /welcome?firstname=Jane&lastname=Doe
    app.Get("/welcome", func(ctx iris.Context) {
        firstname := ctx.URLParamDefault("firstname", "Guest")
        lastname := ctx.URLParam("lastname") // shortcut for ctx.Request().URL.Query().Get("lastname")

        ctx.Writef("Hello %s %s", firstname, lastname)
    })
    app.Listen(":8080")
}
```



### [Multipart/Urlencoded Form](https://www.iris-go.com/docs/#/?id=multiparturlencoded-form)



```
func main() {
    app := iris.Default()

    app.Post("/form_post", func(ctx iris.Context) {
        message := ctx.PostValue("message")
        nick := ctx.PostValueDefault("nick", "anonymous")

        ctx.JSON(iris.Map{
            "status":  "posted",
            "message": message,
            "nick":    nick,
        })
    })
    app.Listen(":8080")
}
```



#### [Another example: query + post form](https://www.iris-go.com/docs/#/?id=another-example-query-post-form)



```
POST /post?id=1234&page=1 HTTP/1.1
Content-Type: application/x-www-form-urlencoded

name=kataras&message=this_is_great
```

```
func main() {
    app := iris.Default()

    app.Post("/post", func(ctx iris.Context) {
        id, err := ctx.URLParamInt("id", 0)
        if err != nil {
            ctx.StopWithError(iris.StatusBadRequest, err)
            return
        }

        page := ctx.URLParamIntDefault("page", 0)
        name := ctx.PostValue("name")
        message := ctx.PostValue("message")

        ctx.Writef("id: %d; page: %d; name: %s; message: %s", id, page, name, message)
    })
    app.Listen(":8080")
}
```

```
id: 1234; page: 1; name: kataras; message: this_is_great
```



#### [Query and post form parameters](https://www.iris-go.com/docs/#/?id=query-and-post-form-parameters)



```
POST /post?id=a&id=b&id=c&name=john&name=doe&name=kataras
Content-Type: application/x-www-form-urlencoded
```

```
func main() {
    app := iris.Default()

    app.Post("/post", func(ctx iris.Context) {

        ids := ctx.URLParamSlice("id")
        names, err := ctx.PostValues("name")
        if err != nil {
            ctx.StopWithError(iris.StatusBadRequest, err)
            return
        }

        ctx.Writef("ids: %v; names: %v", ids, names)
    })
    app.Listen(":8080")
}
```

```
ids: [a b c], names: [john doe kataras]
```



#### 上传文件



##### 单个文件

```
const maxSize = 8 * iris.MB

func main() {
    app := iris.Default()

    app.Post("/upload", func(ctx iris.Context) {
        // Set a lower memory limit for multipart forms (default is 32 MiB)
        ctx.SetMaxRequestBodySize(maxSize)
        // OR
        // app.Use(iris.LimitRequestBodySize(maxSize))
        // OR
        // OR iris.WithPostMaxMemory(maxSize)

        // single file
        file, fileHeader, err:= ctx.FormFile("file")
        if err != nil {
            ctx.StopWithError(iris.StatusBadRequest, err)
            return
        }

        // Upload the file to specific destination.
        dest := filepath.Join("./uploads", fileHeader.Filename)
        ctx.SaveFormFile(file, dest)

        ctx.Writef("File: %s uploaded!", fileHeader.Filename)
    })

    app.Listen(":8080")
}
```



如何执行 `curl`：

```
curl -X POST http://localhost:8080/upload \
  -F "file=@/Users/kataras/test.zip" \
  -H "Content-Type: multipart/form-data"
```



### Multiple File



更多细节请查看示例：[example code](https://github.com/kataras/iris/tree/master/_examples/file-server/upload-files)。



```
func main() {
    app := iris.Default()
    app.Post("/upload", func(ctx iris.Context) {
        files, n, err := ctx.UploadFormFiles("./uploads")
        if err != nil {
            ctx.StopWithStatus(iris.StatusInternalServerError)
            return
        }

        ctx.Writef("%d files of %d total size uploaded!", len(files), n))
    })

    app.Listen(":8080", iris.WithPostMaxMemory(8 * iris.MB))
}
```



使用 `curl` 调用：



```
curl -X POST http://localhost:8080/upload \
  -F "upload[]=@/Users/kataras/test1.zip" \
  -F "upload[]=@/Users/kataras/test2.zip" \
  -H "Content-Type: multipart/form-data"
```



### 路由组



```
func main() {
    app := iris.Default()

    // Simple group: v1
    v1 := app.Party("/v1")
    {
        v1.Post("/login", loginEndpoint)
        v1.Post("/submit", submitEndpoint)
        v1.Post("/read", readEndpoint)
    }

    // Simple group: v2
    v2 := app.Party("/v2")
    {
        v2.Post("/login", loginEndpoint)
        v2.Post("/submit", submitEndpoint)
        v2.Post("/read", readEndpoint)
    }

    app.Listen(":8080")
}
```



### 空白的 Iris  App



将

```
app := iris.New()
```

替换为

```
// Default with "debug" Logger Level.
// Localization enabled on "./locales" directory
// and HTML templates on "./views" or "./templates" directory.
// It runs with the AccessLog on "./access.log",
// Recovery and Request ID middleware already attached.
app := iris.Default()
```



### 使用中间件



```
package main

import (
    "github.com/kataras/iris/v12"

    "github.com/kataras/iris/v12/middleware/recover"
)

func main() {
    // Creates an iris application without any middleware by default
    app := iris.New()

    // Global middleware using `UseRouter`.
    //
    // Recovery middleware recovers from any panics and writes a 500 if there was one.
    app.UseRouter(recover.New())

    // Per route middleware, you can add as many as you desire.
    app.Get("/benchmark", MyBenchLogger(), benchEndpoint)

    // Authorization group
    // authorized := app.Party("/", AuthRequired())
    // exactly the same as:
    authorized := app.Party("/")
    // per group middleware! in this case we use the custom created
    // AuthRequired() middleware just in the "authorized" group.
    authorized.Use(AuthRequired())
    {
        authorized.Post("/login", loginEndpoint)
        authorized.Post("/submit", submitEndpoint)
        authorized.Post("/read", readEndpoint)

        // nested group
        testing := authorized.Party("testing")
        testing.Get("/analytics", analyticsEndpoint)
    }

    // Listen and serve on 0.0.0.0:8080
    app.Listen(":8080")
}
```



应用文件日志



```
func main() {
    app := iris.Default()
    // Logging to a file.
    // Colors are automatically disabled when writing to a file.
    f, _ := os.Create("iris.log")
    app.Logger().SetOutput(f)

    // Use the following code if you need to write the logs
    // to file and console at the same time.
    // app.Logger().AddOutput(os.Stdout)

    app.Get("/ping", func(ctx iris.Context) {
        ctx.WriteString("pong")
    })

   app.Listen(":8080")
}
```



#### 控制日志输出颜色



默认情况下，终端日志输出的颜色，依赖检测到的 TTY。



通常会想要自定义标题，文本颜色和样式。



导入 `golang` 和 `pio`：



```
import (
    "github.com/kataras/golog"
    "github.com/kataras/pio"
    // [...]
)
```



获取一个用于自定义的日志等级。`DebugLevel`：



```
level := golog.Levels[golog.DebugLevel]
```



你可以完全控制它的文本，标题和样式：



```
// The Name of the Level
// that named (lowercased) will be used
// to convert a string level on `SetLevel`
// to the correct Level type.
Name string
// AlternativeNames are the names that can be referred to this specific log level.
// i.e Name = "warn"
// AlternativeNames = []string{"warning"}, it's an optional field,
// therefore we keep Name as a simple string and created this new field.
AlternativeNames []string
// Tha Title is the prefix of the log level.
// See `ColorCode` and `Style` too.
// Both `ColorCode` and `Style` should be respected across writers.
Title string
// ColorCode a color for the `Title`.
ColorCode int
// Style one or more rich options for the `Title`.
Style []pio.RichOption
```



示例代码：



```
level := golog.Levels[golog.DebugLevel]
level.Name = "debug" // default
level.Title = "[DBUG]" // default
level.ColorCode = pio.Yellow // default
```



#### 改变输出格式

```
app.Logger().SetFormat("json", "    ")
```



#### 注册自定义的格式

```
app.Logger().RegisterFormatter(new(myFormatter))
```



[golog.Formatter interface](https://github.com/kataras/golog/blob/master/formatter.go) 接口看起来如下：

```
// Formatter is responsible to print a log to the logger's writer.
type Formatter interface {
    // The name of the formatter.
    String() string
    // Set any options and return a clone,
    // generic. See `Logger.SetFormat`.
    Options(opts ...interface{}) Formatter
    // Writes the "log" to "dest" logger.
    Format(dest io.Writer, log *Log) bool
}
```



改变每一级的输出样式：

```
app.Logger().SetLevelOutput("error", os.Stderr)
app.Logger().SetLevelFormat("json")
```



#### 请求记录



在上面，我们已经看到了使用日志记录一些应用相关的信息和错误。日志的功能不止如此，接下来，我们将看到使用日志记录 HTTP 请求和响应。



```
package main

import (
    "os"

    "github.com/kataras/iris/v12"
    "github.com/kataras/iris/v12/middleware/accesslog"
)

// Read the example and its comments carefully.
func makeAccessLog() *accesslog.AccessLog {
    // Initialize a new access log middleware.
    ac := accesslog.File("./access.log")
    // Remove this line to disable logging to console:
    ac.AddOutput(os.Stdout)

    // The default configuration:
    ac.Delim = '|'
    ac.TimeFormat = "2006-01-02 15:04:05"
    ac.Async = false
    ac.IP = true
    ac.BytesReceivedBody = true
    ac.BytesSentBody = true
    ac.BytesReceived = false
    ac.BytesSent = false
    ac.BodyMinify = true
    ac.RequestBody = true
    ac.ResponseBody = false
    ac.KeepMultiLineError = true
    ac.PanicLog = accesslog.LogHandler

    // Default line format if formatter is missing:
    // Time|Latency|Code|Method|Path|IP|Path Params Query Fields|Bytes Received|Bytes Sent|Request|Response|
    //
    // Set Custom Formatter:
    ac.SetFormatter(&accesslog.JSON{
        Indent:    "  ",
        HumanTime: true,
    })
    // ac.SetFormatter(&accesslog.CSV{})
    // ac.SetFormatter(&accesslog.Template{Text: "{{.Code}}"})

    return ac
}

func main() {
    ac := makeAccessLog()
    defer ac.Close() // Close the underline file.

    app := iris.New()
    // Register the middleware (UseRouter to catch http errors too).
    app.UseRouter(ac.Handler)

    app.Get("/", indexHandler)

    app.Listen(":8080")
}

func indexHandler(ctx iris.Context) {
    ctx.WriteString("OK")
}
```



详细示例请查看 [_examples/logging/request-logger](https://github.com/kataras/iris/tree/master/_examples/logging/request-logger)。



### 模型绑定及验证



可以使用 model binding，来将请求内容绑定为具体的结构类型。当前支持绑定 `JSON`，`JSONProtobuf`，`Protobuf`，`MsgPack`，`XML`，`YAML` 以及标准的 form 键值形式（for=bar&boo=baz）。



```
ReadJSON(outPtr interface{}) error
ReadJSONProtobuf(ptr proto.Message, opts ...ProtoUnmarshalOptions) error
ReadProtobuf(ptr proto.Message) error
ReadMsgPack(ptr interface{}) error
ReadXML(outPtr interface{}) error
ReadYAML(outPtr interface{}) error
ReadForm(formObject interface{}) error
ReadQuery(ptr interface{}) error
```



当使用 `ReadBody` 系列方法，Iris 尝试根据请求的 `Content-Type` 头，来推导请求的类型。如果你明确知道你要绑定的内容，你可以使用 `ReadXXX` 系列方法，来明确返回需要的类型。比如 `ReadJSON` 或者 `ReadProtobuf` 等等。



```
ReadBody(ptr interface{}) error
```



Iris 没有内置数据验证 。然而，它却允许你可以附带一个验证器，该验证器将自动调用 `ReadJSON`，`ReadXML` 等方法。本例中，我们将学习如何使用 `[go-playground/validator/v10](https://www.iris-go.com/docs/(https://github.com/go-playground/validator)) `，来对请求数据进行验证。



需要注意的是，所有你想要绑定的字段，你都需要设置该字段的的 tag。举个例子，当你绑定的是 JSON 结构，设置 `json:fieldname`。



你可以指定某个字段是必须的。当字段的 tag 被标记为 `binding:"required"`时，如果这个字段为空，那么将会报错。



```
package main

import (
    "fmt"

    "github.com/kataras/iris/v12"
    "github.com/go-playground/validator/v10"
)

func main() {
    app := iris.New()
    app.Validator = validator.New()

    userRouter := app.Party("/user")
    {
        userRouter.Get("/validation-errors", resolveErrorsDocumentation)
        userRouter.Post("/", postUser)
    }
    app.Listen(":8080")
}

// User contains user information.
type User struct {
    FirstName      string     `json:"fname" validate:"required"`
    LastName       string     `json:"lname" validate:"required"`
    Age            uint8      `json:"age" validate:"gte=0,lte=130"`
    Email          string     `json:"email" validate:"required,email"`
    FavouriteColor string     `json:"favColor" validate:"hexcolor|rgb|rgba"`
    Addresses      []*Address `json:"addresses" validate:"required,dive,required"`
}

// Address houses a users address information.
type Address struct {
    Street string `json:"street" validate:"required"`
    City   string `json:"city" validate:"required"`
    Planet string `json:"planet" validate:"required"`
    Phone  string `json:"phone" validate:"required"`
}

type validationError struct {
    ActualTag string `json:"tag"`
    Namespace string `json:"namespace"`
    Kind      string `json:"kind"`
    Type      string `json:"type"`
    Value     string `json:"value"`
    Param     string `json:"param"`
}

func wrapValidationErrors(errs validator.ValidationErrors) []validationError {
    validationErrors := make([]validationError, 0, len(errs))
    for _, validationErr := range errs {
        validationErrors = append(validationErrors, validationError{
            ActualTag: validationErr.ActualTag(),
            Namespace: validationErr.Namespace(),
            Kind:      validationErr.Kind().String(),
            Type:      validationErr.Type().String(),
            Value:     fmt.Sprintf("%v", validationErr.Value()),
            Param:     validationErr.Param(),
        })
    }

    return validationErrors
}

func postUser(ctx iris.Context) {
    var user User
    err := ctx.ReadJSON(&user)
    if err != nil {
        // Handle the error, below you will find the right way to do that...

        if errs, ok := err.(validator.ValidationErrors); ok {
            // Wrap the errors with JSON format, the underline library returns the errors as interface.
            validationErrors := wrapValidationErrors(errs)

            // Fire an application/json+problem response and stop the handlers chain.
            ctx.StopWithProblem(iris.StatusBadRequest, iris.NewProblem().
                Title("Validation error").
                Detail("One or more fields failed to be validated").
                Type("/user/validation-errors").
                Key("errors", validationErrors))

            return
        }

        // It's probably an internal JSON error, let's dont give more info here.
        ctx.StopWithStatus(iris.StatusInternalServerError)
        return
    }

    ctx.JSON(iris.Map{"message": "OK"})
}

func resolveErrorsDocumentation(ctx iris.Context) {
    ctx.WriteString("A page that should document to web developers or users of the API on how to resolve the validation errors")
}
```



请求示例



```
{
    "fname": "",
    "lname": "",
    "age": 45,
    "email": "mail@example.com",
    "favColor": "#000",
    "addresses": [{
        "street": "Eavesdown Docks",
        "planet": "Persphone",
        "phone": "none",
        "city": "Unknown"
    }]
}
```



返回数据示例



```
{
    "title": "Validation error",
    "detail": "One or more fields failed to be validated",
    "type": "http://localhost:8080/user/validation-errors",
    "status": 400,
    "fields": [
    {
        "tag": "required",
        "namespace": "User.FirstName",
        "kind": "string",
        "type": "string",
        "value": "",
        "param": ""
    },
    {
        "tag": "required",
        "namespace": "User.LastName",
        "kind": "string",
        "type": "string",
        "value": "",
        "param": ""
    }
    ]
}
```





