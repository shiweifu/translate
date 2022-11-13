先说需求：

- 支持markdown
- 文件结构固定
- 生成 html
- 内容更新后，自动发布
- 支持 article



之前使用 middleman 搭建过一个接近这个需求的博客系统，但流程有点复杂，这次计划引入一个 Web 框架，完成路由和渲染的工作。在 markdown 顶部，引入 meta 信息，通过触发 hook 来实时加载页面。



这种简单的需求，使用轻量级框架最合适了，标准的 Web 框架，默认会带很多我们不需要的功能。我们这里选用的框架是 Roda，它的核心实现，只包括一个路由，其他功能均通过插件来完成，而路由实现部分，也是通过嵌套 proc 来实现的，是个很有特色的 Ruby 框架。



[官网地址](roda.jeremyevans.net)

[Github 地址](https://github.com/jeremyevans/roda)



官网示例：



```
# cat config.ru
require "roda"

class App < Roda
  route do |r|
    # GET / request
    r.root do
      r.redirect "/hello"
    end

    # /hello branch
    r.on "hello" do
      # Set variable for all routes in /hello branch
      @greeting = 'Hello'

      # GET /hello/world request
      r.get "world" do
        "#{@greeting} world!"
      end

      # /hello request
      r.is do
        # GET /hello request
        r.get do
          "#{@greeting}!"
        end

        # POST /hello request
        r.post do
          puts "Someone said #{@greeting}!"
          r.redirect
        end
      end
    end
  end
end

run App.freeze.app
```



可以看到，Roda 自己实现了一套 DSL，用于请求配置，下面简单介绍一下基本语法。



`r.on`：匹配全部的参数，如请求 `/blogs/abc` ，此时 `/blogs` 路由下面的代码就会被匹配到。

`r.is`：匹配全部的参数，但不能有子路由。如 `/blogs/abc` 就只能会被 `/blogs/abc` 路由匹配到。

`r.get/r.post`：匹配 HTTP 请求类型。



这种路由和实现在一起的写法的好处是：



1. 定义和实现在一起，很清晰
2. 根据层级关系嵌套路由，没什么心智成本



当然，需要需要处理的请求很复杂，那么，这种写法就不是很合适了，处理部分的代码一多，就会很混乱。Roda 也支持通过插件来实现传统的 hash route 拆分，这样就可以将不同的路由路由拆到不同的文件里。Roda 的核心实现代码并不多，很多特性都是通过插件来实现的，官方直接提供了大量的插件：



[Roda Plugins](http://roda.jeremyevans.net/documentation.html)



当然，我们这个项目代码量很少，路由这块，只用到最基本写法就可以了。



## 构建基本 App



```
# cat config.ru
require "roda"

class App < Roda
  route do |r|
    r.root do
      "index"
    end
    
    r.on "posts", String, method: :get do |post_title|
    	post_title
    end

    r.on "pages", String, method: :get do |page_title|
      page_title
    end

    r.on "about" do
      r.get do
      	"about page"
      end
    end

  end
end

run App.freeze.app
```





## 视图渲染



网站最终会渲染到浏览器中，供用户使用。Roda 默认只返回 string 类型，通过 render 插件来实现渲染特性。



render 插件的页面：[Roda::RodaPlugins::Render (jeremyevans.net)](http://roda.jeremyevans.net/rdoc/classes/Roda/RodaPlugins/Render.html)



### 引入 render 插件



render 是 Roda 自带的插件，不需要额外引用外部文件或安装 Gem 包，默认已经引入了，只需要在 Roda 项目开始时声明一下即可：



```
plugin :render

route do |r|
  r.is 'foo' do
    view('foo') # renders views/foo.erb inside views/layout.erb
  end

  r.is 'bar' do
    render('bar') # renders views/bar.erb
  end
end
```



可以看到通过调用 `view` 语句，来渲染页并返回。



render 的功能很强大，我们渲染页面时的常用功能：

- 指定模板引擎
- 指定 layout 文件
- 配置视图目录
- 页面缓存



均支持。此外还有大量的配置选项和其他功能，详情可见上面的插件链接。



## 前端编译资源



编译资源这里，分为两部分：

1. JavaScript、CSS 前端资源打包
2. 打包后的资源的 Server



和渲染一样，Roda 的资源响应，也是通过插件来完成的：[Roda::RodaPlugins::Assets (jeremyevans.net)](https://roda.jeremyevans.net/rdoc/classes/Roda/RodaPlugins/Assets.html)

















