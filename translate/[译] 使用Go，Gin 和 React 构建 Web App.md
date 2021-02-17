

翻译自：https://dev.to/codehakase/building-a-web-app-with-go-gin-and-react-5ke



>  本文最初发表自 [My Blog](https://hakaselogs.me/2018-04-20/building-a-web-app-with-go-gin-and-react)



TL;DR：在本文中，我将展示给你使用 Go 和 Gin 框架构建一个 Web 应用程序有多简单，此外这个程序还添加了身份验证。可以访问 GitHub repo  来获取我们将要编写的这个项目的代码。



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



我们将为不同类型的文件创建新的目录。在 `main.go` 文件的目录下，我们创建 `views`文件夹。在 `views` 文件夹中，创建 `js` 文件夹和 `index.html` 文件。



`index.html` 文件的内容如下：



```
<!DOCTYPE html>
<html>
<head>
  <title>Jokeish App</title>
</head>

<body>
  <h1>Welcome to the Jokeish App</h1>
</body>
</html>
```



在我们进行测试之前，我们先安装依赖：



```
$ go get -u github.com/gin-gonic/gin
$ go get -u github.com/gin-gonic/contrib/static
```



我们通过 `go run main.go` 命令来启动服务端，然后验证其是否可以正常工作：



![go-gin-gorun-1](https://res.cloudinary.com/practicaldev/image/fetch/s--xw7zq4UA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/9336187/38358547-700bc822-38bd-11e8-88a6-246559fbe57f.png)



在应用运行起来后，我们在浏览器访问 `http://localhost:3000`。如果一切正常，你将看到一级文本 `Welcome to the Jokeish App` 被显示。



![golang-welcome-de](https://res.cloudinary.com/practicaldev/image/fetch/s--KMuSBeR3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/9336187/38385072-cdaa2168-3908-11e8-9889-e9c1e713ce0a.png)



### 定义 API

让我们添加更多代码在 `main.go` 文件中，用于 API 定义。我们更新我们的 `main` 函数，增加两个路由 `/jokes/` 和 `/jokes/like/:jokeID`，将这两个路由放在 `/api/` 中。



```
func main() {
  // ... leave the code above untouched...

  // Our API will consit of just two routes
  // /jokes - which will retrieve a list of jokes a user can see
  // /jokes/like/:jokeID - which will capture likes sent to a particular joke
  api.GET("/jokes", JokeHandler)
  api.POST("/jokes/like/:jokeID", LikeJoke)
}

// JokeHandler retrieves a list of available jokes
func JokeHandler(c *gin.Context) {
  c.Header("Content-Type", "application/json")
  c.JSON(http.StatusOK, gin.H {
    "message":"Jokes handler not implemented yet",
  })
}

// LikeJoke increments the likes of a particular joke Item
func LikeJoke(c *gin.Context) {
  c.Header("Content-Type", "application/json")
  c.JSON(http.StatusOK, gin.H {
    "message":"LikeJoke handler not implemented yet",
  })
}
```



此时，`main.go` 文件看起来如下：



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
  // Our API will consit of just two routes
  // /jokes - which will retrieve a list of jokes a user can see
  // /jokes/like/:jokeID - which will capture likes sent to a particular joke
  api.GET("/jokes", JokeHandler)
  api.POST("/jokes/like/:jokeID", LikeJoke)

  // Start and run the server
  router.Run(":3000")
}

// JokeHandler retrieves a list of available jokes
func JokeHandler(c *gin.Context) {
  c.Header("Content-Type", "application/json")
  c.JSON(http.StatusOK, gin.H {
    "message":"Jokes handler not implemented yet",
  })
}

// LikeJoke increments the likes of a particular joke Item
func LikeJoke(c *gin.Context) {
  c.Header("Content-Type", "application/json")
  c.JSON(http.StatusOK, gin.H {
    "message":"LikeJoke handler not implemented yet",
  })
}
```



我们再次运行 `go run main.go`，然后访问我们的路由；`http://localhost:3000/api/jokes` 返回 `200 OK`，以及信息 `jokes handler not implemented yet`，POST 请求 `http://localhost:3000/api/jokes/like/1`，返回 `200 OK` ，以及信息 `Likejoke handler not implemented yet`。



### 笑话数据



我们已经定义了路由集合，该路由集合只做一件事，即返回 `JSON` 响应数据，通过向其添加更多代码，我们将为代码库增加一些乐子。



```
// ... leave the code above untouched...

// Let's create our Jokes struct. This will contain information about a Joke

// Joke contains information about a single Joke
type Joke struct {
  ID     int     `json:"id" binding:"required"`
  Likes  int     `json:"likes"`
  Joke   string  `json:"joke" binding:"required"`
}

// We'll create a list of jokes
var jokes = []Joke{
  Joke{1, 0, "Did you hear about the restaurant on the moon? Great food, no atmosphere."},
  Joke{2, 0, "What do you call a fake noodle? An Impasta."},
  Joke{3, 0, "How many apples grow on a tree? All of them."},
  Joke{4, 0, "Want to hear a joke about paper? Nevermind it's tearable."},
  Joke{5, 0, "I just watched a program about beavers. It was the best dam program I've ever seen."},
  Joke{6, 0, "Why did the coffee file a police report? It got mugged."},
  Joke{7, 0, "How does a penguin build it's house? Igloos it together."},
}

func main() {
  // ... leave this block untouched...
}

// JokeHandler retrieves a list of available jokes
func JokeHandler(c *gin.Context) {
  c.Header("Content-Type", "application/json")
  c.JSON(http.StatusOK, jokes)
}

// LikeJoke increments the likes of a particular joke Item
func LikeJoke(c *gin.Context) {
  // confirm Joke ID sent is valid
  // remember to import the `strconv` package
  if jokeid, err := strconv.Atoi(c.Param("jokeID")); err == nil {
    // find joke, and increment likes
    for i := 0; i < len(jokes); i++ {
      if jokes[i].ID == jokeid {
        jokes[i].Likes += 1
      }
    }

    // return a pointer to the updated jokes list
    c.JSON(http.StatusOK, &jokes)
  } else {
    // Joke ID is invalid
    c.AbortWithStatus(http.StatusNotFound)
  }
}

// NB: Replace the JokeHandler and LikeJoke functions in the previous version to the ones above

```



我们的代码看起来很好，让我们接着测试我们的 API。我们可以使用 `curl` 和 `postman`，向 http://localhost:3000/jokes 发送 `GET` 请求，以获取笑话的列表，以及向 `http://localhost:30000/jokes/like/{jokeid}`，增加笑话的喜欢数。

```
$ curl http://localhost:3000/api/jokes

$ curl -X POST http://localhost:3000/api/jokes/like/4
```



### 构建 UI（React）



我们已经有了我们的API，因此让我们构建一个前端来展示来自我们API的数据。 为此，我们将使用React。 我们不会深入研究React，因为它不会超出本教程的范围。 如果您需要了解更多有关React的信息，请查看官方教程。 您可以使用自己喜欢的任何前端框架来实现UI。



### 初始化



我们将编辑index.html文件以添加运行React所需的外部库，然后需要在views / js目录中创建一个app.jsx文件，其中将包含我们的React代码。



我们的 `index.html` 文件此时看起来是这样的：

```
<!DOCTYPE html>
<html>

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Jokeish App</title>
  <script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
  <script src="https://cdn.auth0.com/js/auth0/9.0/auth0.min.js"></script>
  <script type="application/javascript" src="https://unpkg.com/react@16.0.0/umd/react.production.min.js"></script>
  <script type="application/javascript" src="https://unpkg.com/react-dom@16.0.0/umd/react-dom.production.min.js"></script>
  <script type="application/javascript" src="https://unpkg.com/babel-standalone@6.26.0/babel.js"></script>
  <script type="text/babel" src="js/app.jsx"></script>
  <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
</head>

<body>
  <div id="app"></div>
</body>

</html>
```



### 构建我们的组件

在 React 中，视图被拆分为组件。我们需要构建一些组件。一个 `App` 组件作为应用程序的入口，Home 组件用于用户的登录，`LoggedIn` 组件只对已登录用户可见，以及 `Joke` 组件用于显示笑话列表。我们将所有这些组件写入 `app.jsx` 文件中。



### App 组件



本组件作为我们的 React App 的入口。它决定了用户登录或者不登录状态下，哪个组件会被看到。我们首先由一个基础版本开始，然后增加更多功能。



```
class App extends React.Component {
  render() {
    if (this.loggedIn) {
      return (<LoggedIn />);
    } else {
      return (<Home />);
    }
  }
}
```



### Home 组件



本组件在用户非登录状态下显示。以及一个按钮，用于打开锁屏屏幕（我们稍后增加此功能），他们可以在此注册或登录。



```
class Home extends React.Component {
  render() {
    return (
      <div className="container">
        <div className="col-xs-8 col-xs-offset-2 jumbotron text-center">
          <h1>Jokeish</h1>
          <p>A load of Dad jokes XD</p>
          <p>Sign in to get access </p>
          <a onClick={this.authenticate} className="btn btn-primary btn-lg btn-login btn-block">Sign In</a>
        </div>
      </div>
    )
  }
}
```



### 登录后组件



当用户登录后，本组件被显示。当组件被挂载时，存储在 `store` 的笑话数据将被显示。



```
class LoggedIn extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      jokes: []
    }
  }

  render() {
    return (
      <div className="container">
        <div className="col-lg-12">
          <br />
          <span className="pull-right"><a onClick={this.logout}>Log out</a></span>
          <h2>Jokeish</h2>
          <p>Let's feed you with some funny Jokes!!!</p>
          <div className="row">
            {this.state.jokes.map(function(joke, i){
              return (<Joke key={i} joke={joke} />);
            })}
          </div>
        </div>
      </div>
    )
  }
}
```



