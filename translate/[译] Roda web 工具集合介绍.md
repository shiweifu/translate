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

