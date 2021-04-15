翻译自 Echo 官方教程：https://echo.labstack.com/guide/



安装Echo，需要 Golang 的版本大于等于 1.13。Go 1.12 的支持有限，某些中间件不可用。确保您的项目目录在 `$GOPATH` 之外。



```
$ mkdir myapp && cd myapp
$ go mod init myapp
$ go get github.com/labstack/echo/v4
```



如果你的 Go 版本小于等于 1.14，需要执行下面的语句：



```
$ GO111MODULE=on go get github.com/labstack/echo/v4
```



### Hello, World!



创建 `server.go` 文件：



```
package main

import (
	"net/http"
	
	"github.com/labstack/echo/v4"
)

func main() {
	e := echo.New()
	e.GET("/", func(c echo.Context) error {
		return c.String(http.StatusOK, "Hello, World!")
	})
	e.Logger.Fatal(e.Start(":1323"))
}
```



启动服务器：



```
$ go run server.go
```



访问 `http://localhost:1323`，你将看到 `Hello, World` 被正确输出。



### 路由



```
e.POST("/users", saveUser)
e.GET("/users/:id", getUser)
e.PUT("/users/:id", updateUser)
e.DELETE("/users/:id", deleteUser)
```



### 路径参数



```
// e.GET("/users/:id", getUser)
func getUser(c echo.Context) error {
  	// User ID from path `users/:id`
  	id := c.Param("id")
	return c.String(http.StatusOK, id)
}
```



访问 `http://localhost:1323/users/joe` 你将看到参数被正确解析，看到 `Joe` 被输出到页面上。



### 查询参数 

`/show?team=x-men&member=wolverine`



```
//e.GET("/show", show)
func show(c echo.Context) error {
	// Get team and member from the query string
	team := c.QueryParam("team")
	member := c.QueryParam("member")
	return c.String(http.StatusOK, "team:" + team + ", member:" + member)
}
```



此时访问 http://localhost:1323/show?team=x-men&member=wolverine，你将看到 ‘team:x-men, member:wolverine’ 在当前页面。

###  

### 发送 `application/x-www-form-urlencoded`

```
POST /save
```

| name  | value                                       |
| :---- | :------------------------------------------ |
| name  | Joe Smith                                   |
| email | [joe@labstack.com](mailto:joe@labstack.com) |

```go
// e.POST("/save", save)
func save(c echo.Context) error {
	// Get name and email
	name := c.FormValue("name")
	email := c.FormValue("email")
	return c.String(http.StatusOK, "name:" + name + ", email:" + email)
}
```

执行下面的命令：

```sh
$ curl -F "name=Joe Smith" -F "email=joe@labstack.com" http://localhost:1323/save
// => name:Joe Smith, email:joe@labstack.com
```



### 发送 `multipart/form-data` 类型的数据

```
POST /save
```

| name   | value     |
| :----- | :-------- |
| name   | Joe Smith |
| avatar | avatar    |

```go
func save(c echo.Context) error {
	// Get name
	name := c.FormValue("name")
	// Get avatar
  	avatar, err := c.FormFile("avatar")
  	if err != nil {
 		return err
 	}
 
 	// Source
 	src, err := avatar.Open()
 	if err != nil {
 		return err
 	}
 	defer src.Close()
 
 	// Destination
 	dst, err := os.Create(avatar.Filename)
 	if err != nil {
 		return err
 	}
 	defer dst.Close()
 
 	// Copy
 	if _, err = io.Copy(dst, src); err != nil {
  		return err
  	}

	return c.HTML(http.StatusOK, "<b>Thank you! " + name + "</b>")
}
```

执行下面命令：

```
$ curl -F "name=Joe Smith" -F "avatar=@/path/to/your/avatar.png" http://localhost:1323/save
// => <b>Thank you! Joe Smith</b>
```

查看刚刚上传的图片：

```
cd <project directory>
ls avatar.png
// => avatar.png
```

