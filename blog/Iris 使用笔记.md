### 路由

分组：

```
   booksAPI := app.Party("/books")
    {
        // GET: http://localhost:8080/books
        booksAPI.Get("/", list)
        // POST: http://localhost:8080/books
        booksAPI.Post("/", create)
    }
```

带参数：

参数类型
```
Param Type	Go Type	Validation	Retrieve Helper
:string	string	anything (single path segment)	Params().Get
:uuid	string	uuidv4 or v1 (single path segment)	Params().Get
:int	int	-9223372036854775808 to 9223372036854775807 (x64) or -2147483648 to 2147483647 (x32), depends on the host arch	Params().GetInt
:int8	int8	-128 to 127	Params().GetInt8
:int16	int16	-32768 to 32767	Params().GetInt16
:int32	int32	-2147483648 to 2147483647	Params().GetInt32
:int64	int64	-9223372036854775808 to 9223372036854775807	Params().GetInt64
:uint	uint	0 to 18446744073709551615 (x64) or 0 to 4294967295 (x32), depends on the host arch	Params().GetUint
:uint8	uint8	0 to 255	Params().GetUint8
:uint16	uint16	0 to 65535	Params().GetUint16
:uint32	uint32	0 to 4294967295	Params().GetUint32
:uint64	uint64	0 to 18446744073709551615	Params().GetUint64
:bool	bool	"1" or "t" or "T" or "TRUE" or "true" or "True" or "0" or "f" or "F" or "FALSE" or "false" or "False"	Params().GetBool
:alphabetical	string	lowercase or uppercase letters	Params().Get
:file	string	lowercase or uppercase letters, numbers, underscore (_), dash (-), point (.) and no spaces or other special characters that are not valid for filenames	Params().Get
:path	string	anything, can be separated by slashes (path segments) but should be the last part of the route path	Params().Get
```

params

```
    app.Get("/user/{name}", func(ctx iris.Context) {
        name := ctx.Params().Get("name")
        ctx.Writef("Hello %s", name)
    })
```

queryString

```
    app.Get("/welcome", func(ctx iris.Context) {
        firstname := ctx.URLParamDefault("firstname", "Guest")
        lastname := ctx.URLParam("lastname") // shortcut for ctx.Request().URL.Query().Get("lastname")

        ctx.Writef("Hello %s %s", firstname, lastname)
    })
```




MVC 版：

```
import "github.com/kataras/iris/v12/mvc"

m := mvc.New(booksAPI)
m.Handle(new(BookController))
```

### 生成接口文档

使用 yaag。

初始化：

```
	yaag.Init(&yaag.Config{ // <- IMPORTANT, init the middleware.
		On:       true,
		DocTitle: "Iris",
		DocPath:  "apidoc.html",
		BaseUrls: map[string]string{"Production": "", "Staging": ""},
	})
```

使用中间件：

```
	app.Use(irisyaag.New()) // <- IMPORTANT, register the middleware.
```



### MVC



Iris 的 MVC 根据定义的 Controller 中的方法，来自动推导出路由名称和参数。



```

type ApiController struct {
	Ctx iris.Context
}

func (p *ApiController) Get() *iris.Map {
	r := iris.Map{
		"name": "zhangsan",
		"age":  5,
	}

	return r
}
```



结构体中的 Ctx 无需赋值，会自动绑定值。也会根据参数来推导出要绑定的路由参数。

```
**`func(*Controller) Get() - GET:/user`**
**`func(*Controller) Post() - POST:/user`**
**`func(*Controller) GetLogin() - GET:/user/login`**
**`func(*Controller) PostLogin() - POST:/user/login`**
**`func(*Controller) GetProfileFollowers() - GET:/user/profile/followers`**
**`func(*Controller) PostProfileFollowers() - POST:/user/profile/followers`**
**`func(*Controller) GetBy(id int64) - GET:/user/{param:long}`**
**`func(*Controller) PostBy(id int64) - POST:/user/{param:long}`**
```



自定义路由：

通过 Controller 的 `BeforeActivation` 来进行定义。自定义路由不用遵循 Controller 命名约定：Method + Name + Params，可以自由命名。



```
func (p *ApiController) BeforeActivation(b mvc.BeforeActivation) {
	b.Handle("GET", "/custom/{id:long}", "MyCustomHandler")
}


func (p *ApiController) MyCustomHandler(id int64) string {
	return "MyCustomHandler says Hey"
}
```



Controller 还有一个 AfterActivation 方法，可以做一些清理工作。



配合 Party，可以实现定义子路由的效果：



```
	mvc.New(app.Party("/api/common")).Handle(&controllers.CommonController{})
	mvc.New(app.Party("/api/user")).Handle(&controllers.UserController{})
```



也可以通过 Controller 单独配置 Middleware。



### 模板



Iris 支持多种主流模板，只需要配置目录，即可使用：

```
func setupTemplate(app *iris.Application) {
	// eng := iris.Django("./views", ".html").Reload(true)
	eng := iris.Django("./views", ".html").Reload(true)
	eng.AddFunc("titleFunc", func(pt ...string) string {
		if len(pt) == 0 {
			return os.Getenv("TITLE")
		}
		return fmt.Sprintf("%s - %s", pt[0], os.Getenv("TITLE"))
	})
	app.RegisterView(eng)
}
```







 

