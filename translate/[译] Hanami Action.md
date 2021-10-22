一个 action，是一个端点，用于处理 HTTP 请求，路由表将请求地址和 action 绑定在一起。在 Hanami 应用中，一个 action 是一个对象，而控制器是对他们进行分组的 Ruby 模块。



这种设计，提供了自包含 action，不会意外的与其他 action 共享上下文。还可以防止出现体积巨大的控制器。它在操作和控制方面，有以下几个优点：



### 一个简单 Action



Hanami 自带 action 生成器。让我们创建一个新的：



```
$ hanami generate action web dashboard#index
```



让我们解释这个 action：



```
# apps/web/controllers/dashboard/index.rb
module Web
  module Controllers
    module Dashboard
      class Index
        include Web::Action

        def call(params)
        end
      end
    end
  end
end
```



#### 命名



这个文件开始于一个模块定义。



第一个令牌是我们的应用程序的名称：Web。Hanami可以在同一Ruby进程中运行多个应用程序。它们位于应用程序/。他们的名字用作顶级模块，以包含类似于动作和视图的内部组件，以避免命名冲突。如果我们在Application Admin下有另一个动作主页:: index，其中两个可以在同一代码库内部共存。



第二个令牌是一个常规名称:Controllers。所有的控制器都嵌套在它下面。当应用程序启动时，这个模块为我们在运行时生成。



> 对于名为Web的给定应用程序，可以在Web:: controllers下使用控制器。



最后一位是Dashboard，它是我们的控制器。



整个动作的名称是Web::Controllers::Dashboard::Index。



你应该避免给你的动作模块与你的应用程序相同的名称，例如，避免在一个名为Web的应用程序中给控制器命名为Web。如果你有一个像Web::Controllers::Web这样的控制器名，那么你的应用程序中的一些代码就会因为没有找到常量而崩溃，例如在包含Web::Layout的视图中。这是因为Ruby从当前模块开始常量查找，所以Web::Controllers::Web或Web::Controllers::Web::MyAction模块中的代码引用的Web::Layout这样的常量将被转换为Web::Controllers::Web:: Web::布局，无法找到并导致该结果。

