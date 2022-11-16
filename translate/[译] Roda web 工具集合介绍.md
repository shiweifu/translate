## 介绍



当前，有数个 “轻量级” Ruby web & API 库。Sinatra 是其中最知名的一个，除此之外，还有一些其他的库，更新，更快，更整洁，更灵活，或者维护的更好。



[*Roda*](https://roda.jeremyevans.net/) 是其中的一个，它由 Jeremy Evans 创建，他也是 Sequel gem 的作者。起初，它 fork 自 `Cuba`，但当前，已经经过了许多进化。值得称道的是它的执行速度和插件系统，Sequel 中也有类似的特性，在其中，一切都可以被覆盖。



它被定义为 `路由树 Web 工具包`。为什么呢？因为它独特的路由定义方式，和执行方式。让我们看一个例子：



```
require "roda"

class App < Roda
  plugin :render
  plugin :all_verbs

  route do |r|
    r.root do
      view :index
    end
    r.is "artist", Integer do |artist_id|
      @artist = Artist[artist_id]
      check_access(@artist)
      r.get do
        view :artist
      end
      r.post do
        @artist.update(r.params["artist"])
        r.redirect
      end
      r.delete do
        @artist.destroy
        r.redirect  "/"
      end
    end
  end
end
```



如果你与其他库相比较，如 Sinatra，相较于在每个路由中，都分别赋值 `@artist`，并检查值，Roda 的做法是在上级路由树中，赋值一次，下面的子分支路由中，均可访问该变量。这就是 Roda 为什么被称为树状 Web 工具包。



关于路由灵活性的更多例子：



```
route do |r|
    # array matchers
    r.on %w[hello hi]do |greeting|
      "#{greeting}, world!"
    end
    # method matchers
    r.is  "hello", method: [:post, :put, :patch] do
      "All your base are belong to us"
    end
    # Regexp matchers
    r.on  "users" do
      r.get /(\d+)/do |user_id|
        "Your id is #{user_id}"
      end
    end
  end
```



Roda 的插件系统，与 Sequel gem 共享，但也被复制到其他项目，如  [Shrine](https://shrinerb.com/)，和 [Cuba(v3)](http://cyx.is/cuba-3-released.html)。



你的应用可以从小型的，紧凑的开始，随着时间推移，增加复杂性，并伴随着学习，对应用的特性和性能，有精细的控制。



例如，你需要 [websockets](https://www.rubydoc.info/gems/roda/2.13.0/Roda/RodaPlugins/Websockets)，请查看：



```
plugin :websockets

route do |r|
  r.get "room" do
    # Matches if it's a websocket request
    r.websocket do |ws|
      ws.on(:message) { ... }
      ws.on(:close) { ... }
      # ...
    end
    # If the request is not a websocket request, we render a template
    view "room"
  end
end
```



你需要 JSON API？`json` [plugin](https://www.rubydoc.info/gems/roda/Roda/RodaPlugins/Json)，自动响应数组和 hash：



```
plugin :json

route do |r|
  r.root do
    [1, 2, 3]
  end
  r.is "foo" do
    { "a" => "b"}
  end
end
```



但你可以轻松的定制化，举个例子，项目添加 `ActiveRecord`：



```
plugin :json, classes: [Array, Hash, ActiveRecord::Base, ActiveRecord::Relation],
  serializer: proc { |object|
    case object
    when Array, Hash
      object.to_json
    else
      Serializer.new(object).as_json
    end
  }

route do |r|
  r.get "albums/recent" do
    Album.recent
  end
  r.get "albums/:id" do |id|
    Album.find(id)
  end
end
```



完整的插件列表数量非常庞大，超过90个，还有一些社区插件，基本上，您所用到的内容，这些插件都已涵盖。如果您想做一个插件，下面是基本结构：



```
class Roda
  module RodaPlugins
    module MyPlugin
      module ClassMethods
      end
      module InstanceMethods
      end
      module RequestClassMethods
      end
      module RequestMethods
      end
      module ResponseClassMethods
      end
      module ResponseMethods
      end
    end
    register_plugin(:my_plugin, MyPlugin)
  end
end
```



## 性能



一图胜千言。下面这张图，表示了每秒请求数，和内存使用情况：

![img](https://res.cloudinary.com/practicaldev/image/fetch/s--KiViLwid--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/bvWRMqG/image1.png)



性能表现非常接近原始 Rack 的值，但是，这是包含 DSL 的。



## 维护情况



Jeremy 在邮件列表中的回复很快，并且他解决了一切 github repo 中反馈的问题：



![img](https://res.cloudinary.com/practicaldev/image/fetch/s--gwfMV-Ko--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/gSnnpzz/image2.png)

（如图所示，所有问题都已处理）



## 一些劣势



-  没有路由列表功能（尽管通过[插件](https://github.com/jeremyevans/roda-route_list) 可以实现）
- 更少的 gem 支持，许多与默认框架绑定
- 相较于 Rails 或者 Sinatra，社区支持更少
- 重新发明一些轮子，不可避免
- 这种风格的路由，也许不是你的风格（但通过该 [插件](http://roda.jeremyevans.net/rdoc/classes/Roda/RodaPlugins/ClassLevelRouting.html) 可以实现 Sinatra 的路由定义风格）



## 扩展链接

- 这是一本不错的书：[Roda book](https://fiachetti.gitlab.io/mastering-roda/)
- 这是官方 [mailing list](https://groups.google.com/forum/#!forum/ruby-roda)
- 如果你想了解更多关于这个插件系统模式的信息，请查看 [Shrine](https://github.com/shrinerb/shrine) 作者的[这篇文章](https://twin.github.io/the-plugin-system-of-sequel-and-roda/)。
- 其他测试：[ruby web frameworks micro benchmarks](https://github.com/luislavena/bench-micro) 项目，著名的 [TechEmpower web benchmarks](https://www.techempower.com/benchmarks/#section=data-r18&hw=ph&test=json&l=zijxtr-f) （Ruby frameworks 实现的过滤器）和 [另一个测试项目](https://github.com/jeremyevans/r10k/).



















