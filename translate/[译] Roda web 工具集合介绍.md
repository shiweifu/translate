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













