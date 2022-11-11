先说需求：

- 支持markdown
- 文件结构固定
- 生成 html
- 内容更新后，自动发布
- 支持 article



之前使用 middleman 搭建过一个接近这个需求的博客系统，但流程有点复杂，这次计划引入一个 Web 框架，完成路由和渲染的工作。在 markdown 顶部，引入 meta 信息，通过触发 hook 来实时加载页面。



这种简单的需求，使用轻量级框架最合适了，我们这里选用的框架是 Roda，是个很有特色的 Ruby 框架，将路由和实现放在了一起。



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



可以看到，Roda 自己实现了一套 DSL，用于请求配置，下面简单介绍一下。



`r.on`：匹配全部的参数，如请求 `/blogs/abc` ，此时 `/blogs` 路由下面的代码就会被匹配到。

`r.is`：匹配全部的参数，但不能有子路由。如 `/blogs/abc` 就只能会被 `/blogs/abc` 路由匹配到。

`r.get/r.post`：匹配 HTTP 请求类型。









