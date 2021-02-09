

翻译自：https://dev.to/codehakase/building-a-web-app-with-go-gin-and-react-5ke



>  本文最初发表自 [My Blog](https://hakaselogs.me/2018-04-20/building-a-web-app-with-go-gin-and-react)



`TL;DR：`在本文中，我将展示给你使用 Go 和 Gin 框架构建一个 Web 应用程序有多简单，此外这个程序还添加了身份验证。可以访问 GitHub [repo](https://github.com/codehakase/golang-gin)  来获取我们将要编写的这个项目的代码。



`Gin` 是一个高性能微型框架，只包含用于构建 Web 应用的必不可少的特性，库和以及方法。它使模块化，可复用变得很简单。这使得您可以使用它编写处理单一请求、多个请求以及一组请求的中间件。



### Gin 特性



`Gin`是执行迅速，功能齐全，使用简单，且开发效率高。下面是它的一些特性，这些特性也解释了为什么它是值得考虑用于下一个 Golang 项目的框架。



- 速度：速度是 Gin 的一个构建目标。框架的路由，基于 Radix 树算法，内存占用少，没有反射，API 性能可预测。



- 可靠：Gin 具有捕获运行时错误和异常的能力，并且有从错误中恢复过来的能力，这确保你的在线应用的可靠性。



- 路由：Gin 提供路由接口，定义路由的表达式具有描述性，看起来很直观。



- JSON 验证：Gin 提供容易的方式来解析并验证 JSON 请求，可以来检查所需要的值。



- 错误处理：Gin 提供了方便的方式来处理整个 HTTP 请求过程中出现的错误。它也可以通过中间层来将这些错误信息写入到日志文件、数据库或者通过网络发送出去。



- 渲染：Gin 为 JSON，XML 以及 HTML 的渲染提供了易于使用的API。



### 准备 



跟随本教程之前，你需要完成 Go 的安装，确保浏览器可以访问 App，以及可以正常执行命令。



`Go`，全称为 Golang，是一个 Google 开发的编程语言，用于构建现代软件。Go的设计目标为开发效率和执行快速。下面是一些 Go 包括的特性：



- 强类型和垃圾回收
- 快速编译时间
- 内置并行开发
- 丰富的标准库



可以由[官网下载](https://golang.org/dl/)， 将 Go 的运行时安装在机器上。



### 使用 Gin 构建应用



我们将通过 `Gin` 构建一个简单的笑话列表应用。我们的应用程序将仅列出一些愚蠢爸爸系列的笑话。我们将向其中添加身份验证，所有登录的用于有权查看和查看笑话。



这将展示给我们使用 Gin 来开发 Web 应用程序，或者 API。



![golang-gin-demo-shot](https://res.cloudinary.com/practicaldev/image/fetch/s--78Nii8DR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/9336187/38371873-6ccabb50-38e5-11e8-9b67-b97cc2ce98c6.png)



我们将使用 Gin，完成以下功能：



- 中间件
- 路由
- 路由组



### 准备开始



我们在 `main.go` 文件中，编写我们的整个 Go 应用。当它的规模还很小时，可以简单的进行构建，并通过 `go run` 在终端中执行。



我们创建新的目录，`golang-gin` 在 Go 工作环境，然后创建 `main.go` 文件：

```
$ mkdir -p $GOPATH/src/github.com/user/golang-gin
$ cd $GOPATH/src/github.com/user/golang-gin
$ touch main.go
```

当前 `main.go` 文件的内容是：

```
package main

import (
  "net/http"

  "github.com/gin-gonic/contrib/static"
  "github.com/gin-gonic/gin"
)

func main() {
  // Set the router as the default one shipped with Gin
  router := gin.Default()

  // Serve frontend static files
  router.Use(static.Serve("/", static.LocalFile("./views", true)))

  // Setup route group for the API
  api := router.Group("/api")
  {
    api.GET("/", func(c *gin.Context) {
      c.JSON(http.StatusOK, gin.H {
        "message": "pong",
      })
    })
  }

  // Start and run the server
  router.Run(":3000")
}
```











