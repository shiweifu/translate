Golang Web 开发，主流是类似 Flask，Sinatra 的微框架，这是因为 Golang 的 Web 相关的包比较完善，可以说到手就是毛坯房了，不同框架根据不同的哲学，进行装修和爆改，房屋结构基本不会动。而这些框架也大同小异。



Buffalo 是一个 Rails Like 的 Golang Web 框架，Rails 这种全功能框架和微型框架的区别，就像毛坯房还是那个毛坯房，普通框架，交付的是精装后的，而 Rails 交付的房子，除了基础居住功能以外，还给住户搭了个车库，修了个小院。强调拎包入住，恨不得夏天床上的凉席都给住户置办了。



我使用 Rails 开发过几个项目，感觉不错，不满主要是语言层面上。



Ruby 的语法虽然写着又快又爽，但是在阅读项目代码的时候，并不是那么友好。使用 Rails 的时候，偶尔想看一下某些部分的实现，无法直接跳转过去来查看，往往需要再开个开发环境，然后通过全局搜索关键字，来找到实现。能直接找到，还是好的，Ruby 语言的【魔法】也很多，也是代码阅读的干扰。



Buffalo 的设计哲学继承自 Rails，所以他也是个【大而全】的框架。特性包括：



- Session
- 后台 Admin
- ORM
- Cookie
- 资源打包
- Webpack
- Database Migration
- Task
- Auth
- Mail
- 国际化
- 类似 ERB 的模板引擎



因为是基于 Go 语言，语言带来的好处当然也是具备的。



### Context



Context 是整个框架非常基础的结构封装，Buffalo 的 Context 是基于 Go 语言内置的 Context 的：



Buffalo Context：



```
type Context interface {
  context.Context
  Response() http.ResponseWriter
  Request() *http.Request
  Session() *Session
  Cookies() *Cookies
  Params() ParamValues
  Param(string) string
  Set(string, interface{})
  LogField(string, interface{})
  LogFields(map[string]interface{})
  Logger() Logger
  Bind(interface{}) error
  Render(int, render.Renderer) error
  Error(int, error) error
  Redirect(int, string, ...interface{}) error
  Data() map[string]interface{}
  Flash() *Flash
  File(string) (binding.File, error)
}
```



可以将 Context 看作一个制造图纸，框架的主体部分就是对这整个接口的实现。



接下来我们按图索骥，先把这里面的方法梳理一下。



Buffalo 默认实现的结构体名：



```
type DefaultContext struct {
	context.Context
	response    http.ResponseWriter
	request     *http.Request
	params      url.Values
	logger      Logger
	session     *Session
	contentType string
	data        *sync.Map
	flash       *Flash
}
```

#### Set(string, interface{})



这个方法用来将变量注入到模板中，然后进行渲染的。页面的辅助函数也是通过这个方法进行注入。



### 周边设施



#### 模板语言



Buffalo 没有使用 Golang 默认的模板，而是自己开发了一套与 Rails 内置的 ERB 类似的模板语言，称为 Plush：[gobuffalo/plush: The powerful template system that Go needs (github.com)](https://github.com/gobuffalo/plush)。



GitHub 中的示例：



```
<html>
<%= if (names && len(names) > 0) { %>
	<ul>
		<%= for (n) in names { %>
			<li><%= capitalize(n) %></li>
		<% } %>
	</ul>
<% } else { %>
	<h1>Sorry, no names. :(</h1>
<% } %>

<%= this is a comment %>
</html>
```



和 erb 一样，都是使用 `<% %>` 来嵌入要执行的代码的，`<%= %>` 嵌入的是需要返回的代码。

区别是：

- 循环语句和逻辑判断语句，变成了 Go 的语法。
- 代码块由 `do..end` 变成了 `{}`



更多使用示例，可以查看 Plush 的 Github 主页。



#### ORM



Buffalo 内置的 ORM 是 POP，一套与 Rails 中 ActiveRecord 类似的 ORM 组件。同样也提供了一个命令行工具：Soda。

POP 封装自 Golang 生态非常流行的数据库操作库 https://github.com/jmoiron/sqlx，在其之上，增加了ORM CRUD 操作，migration 等特性。



Pop 支持的数据库：

- [PostgreSQL](https://www.postgresql.org/) (>= 9.3)
- [CockroachDB](https://www.cockroachlabs.com/) (>= 2.1.0)
- [MySQL](https://www.mysql.com/) (>= 5.7)
- [SQLite3](https://sqlite.org/) (>= 3.x)



POP 的一些概念，与 Active Record 很相似：



- 表必须包含 `id` 列，在模型结构体中，对应 `ID` 字段
- 如果包含 `created_at` 列，且结构体包含 `CreatedAt time.Time` 字段，则在对象创建时，会自动赋值当前时间戳

- 如果包含 `updated_at` 列，且结构体包含 `UpdatedAt time.Time` 字段，则在对象更新时，会自动赋值当前时间戳
- 默认数据库表名，是全小写的复数，如果有多个单词，使用下划线分割。与之对应驼峰命名的结构体。比如 `User{}` 与 `users`，`FooBar` 与 `foo_bars`。



Buffalo 深度集成 Pop，你可以使用这些工具，很容易的构建符合标准的数据库。当然你也可以完全不用它，根据你的喜好，来选择数据库工具。





#### 前端



当前的主流，是前后端分离，Web 后端提供 API 接口，然后前端打包剩下的一切。然而对于许多人手不够的团队和项目来说，全栈的 Web 框架仍然是首选。于是，与前端技术的集成情况，应该是评估一个 Web 框架的很重要的部分。



Buffalo 在这方面做的不错，基本做到开箱即用。这也是他吸引我的地方。不然我为什么不用 Gin、Echo 那种轻量级框架，然后自己集成呢？



默认情况下，通过命令生成的 Buffalo 项目，已经集成了：



- [jQuery](https://jquery.com/)
- [Bootstrap 4](http://getbootstrap.com/)
- [jQuery UJS](https://github.com/rails/jquery-ujs)
- SCSS
- Webpack 及配置文件
- [Font Awesome](http://fontawesome.io/)
- 资源编译



对于很多项目，有了这些主流的工具，基本马上就可以开干了。



因为集成了 Webpack，即使你们团队所使用的技术是这些技术栈之外的技术，只需要简单的配置，也可以无缝使用。



假设我们前端框架想使用更轻量级的 `Bulma`，DOM 操作想使用 `Alpine.js`：



1. 执行 `yarn add bulma` 以及 `yarn add alpinejs`，添加所需要的包
2. 打开 `application.js` 文件，在其中引入 `alpine.js`：`require("alpinejs/dist/alpine.js");`。此时也可以看到，Buffalo 使用 `expose-loader` 引入了 jQuery。
3. 打开 `application.scss` 文件，在其中引入 `bulma`，并删除 `bootstrap` 的引用：`@import "~bulma/bulma.sass";`。仍然保留对 `FontAwesome` 的引用。



再次执行 buffalo dev，此时我们的环境已经准备好对刚刚引入的两个前端库的使用，丝般顺滑。



接下来，按照我们所需要的方式，继续将样式写入到 `application.scss`，将 JS 写入`application.js` 文件即可。在 Buffalo 中，集成这些库比 Rails 的 Webpacker 更加简单。