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



Buffalo 没有使用 Golang 默认的模板，而是自己开发了一套与 Rails 内置的 ERB 类似的模板语言：[gobuffalo/plush: The powerful template system that Go needs (github.com)](https://github.com/gobuffalo/plush)。Github 页面上有基本的使用。





