

翻译自：https://dev.to/codehakase/building-a-web-app-with-go-gin-and-react-5ke



>  本文最初发表自 [My Blog](https://hakaselogs.me/2018-04-20/building-a-web-app-with-go-gin-and-react)



`TL;DR：`在本文中，我将展示给你使用 Go 和 Gin 框架构建一个 Web 应用程序有多简单，此外这个程序还添加了身份验证。可以访问 GitHub [repo](https://github.com/codehakase/golang-gin)  来获取我们将要编写的这个项目的代码。



`Gin` 是一个高性能微型框架，只包含用于构建 Web 应用的必不可少的特性，库和以及方法。它使模块化，可复用变得很简单。这使得您可以使用它编写处理单一请求、多个请求以及一组请求的中间件。



### Gin 特性



`Gin`是执行快速，功能齐全但使用简单，以及有效的 Go Web 框架。下面是它的一些特性，这些特性也解释了为什么它是值得考虑用于下一个 Golang 项目的框架。



- 速度：Gin 是为了提高速度而设计的。 该框架提供了基于Radix树的路由，较小的内存占用空间。 没有反思。 可预测的API性能。



- 稳定：Gin具有在运行时捕获崩溃或崩溃的功能，并且可以从中恢复，因此您的应用程序将始终可用。



- 路由：Gin提供了路由接口，可让您表达Web应用程序或API路由的外观。



- JSON 验证：Gin可以轻松解析和验证JSON请求，检查是否存在必需的值。



- 错误处理：Gin提供了一种方便的方法来收集HTTP请求期间发生的所有错误。 最终，中间件可以将它们写入日志文件，数据库中并通过网络发送。



- 渲染：Gin为JSON，XML和HTML渲染提供了易于使用的API。

