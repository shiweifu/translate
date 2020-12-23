翻译自：https://viewcomponent.org/



### view_component



一个用于 Rails 中的，构建可复用，可测试，以及具有良好封装的视图组件。



### 设计哲学

ViewComponent 设计旨在以最小的代价，无缝的集成到 Rails 中。



### 兼容性



ViewComponent 在 Rails 6.1 中，原生提供支持，可以通过 [monkey patch](https://github.com/github/view_component/blob/main/lib/view_component/render_monkey_patch.rb) 的方式，在 Rails 5 中使用。

ViewComponent 在 Ruby 2.4+ 和 Rails 5+，通过测试。



### 安装

在 `Gemfile` 中，添加：

```
gem "view_component", require: "view_component/engine"
```



### 手册



#### 什么是组件？

ViewComponents 是用于输出 HTML 的 Ruby 对象。使用演示者模式进行实现，灵感来自 React。



#### 何时我该使用组件？

涉及到视图代码的情况下，大多数时候，使用组件都会在复用和测试方面，得到好处。



#### 为什么应该使用组件？



##### 测试

与传统 Rails 视图不一样，ViewComponent 可以进行单元测试。在 Github 的代码仓库，组件的单元测试每个大约花费 25 毫秒，而控制器的测试大约需要 6 秒。



Rails 的视图通常使用慢速集成进行测试，该慢速集成测试除了在视图之外还使用路由和控制器曾。这部分通常不鼓励进行完全的测试覆盖。



对于 ViewComponent，集成测试可以使用端对端的断言，并在单元级别覆盖。



##### 数据流



传统 Rails 视图有一个隐式接口，使得难以推理需要呈现哪些信息，从而在不同上下文中，呈现同一视图，会导致细微的错误。











