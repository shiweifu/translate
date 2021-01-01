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



传统 Rails 视图有一个隐式的接口，这使得那些需要被呈现的信息，难以被呈现，而在不同上下文中呈现同一视图时会导致细微的错误。



ViewComponent 使用标准的 Ruby 初始化，明确定义什么是需要被渲染的，相较于片段方式，可以确保更加容易的被复用。



##### 性能



基于我们的[测试](https://viewcomponent.org/performance/benchmark.rb)，ViewComponent 相较于片段方式，有十倍的速度提升。



##### 标准



视图常常不符合 Ruby 代码的质量标准：方法过长，深层条件嵌套，以及一些因为随意编写而变得神秘的东西。

ViewComponet 是 Ruby 对象，它可以以符合 Ruby 代码标准的质量来编写。



#### 构建组件



##### 约定



组件是 `ViewComponent::Base` 的子类，保存在 `app/components` 目录中。在我们的实践中，它通常不直接继承自 `ViewComponent::Baes`，而是继承自 `ApplicationComponent`，该类也是从 `ViewComponent::Base` 承的。

组件的名字通常以 `Component` 作为结尾。

组件的模块通常使用复数，比如控制器和作业的例子：`Users::AvatarComponent`。

组件的名称应该描述他们的用途，而不是他们被用在哪里（应用 `AvatarComponent`代替`UserComponent`）。



##### 快速开始



可以使用组件生成器来创建一个新的 ViewComponent。

通过传递 ViewComponent 的名称和打算使用的参数给生成器来进行创建：



```
bin/rails generate component Example title content
      invoke  test_unit
      create  test/components/example_component_test.rb
      create  app/components/example_component.rb
      create  app/components/example_component.html.erb
```



ViewComponent 支持生成的模板包括 `erb`，`haml` 和 `slim` 三种模板引擎，默认的模板引擎通过 `config.generators.template_engine` 来指定。



所使用的模板引擎也可以在命令行中通过参数来指定。



```
bin/rails generate component Example title content --template-engine slim
```



##### 实现



视图组件是一个 Ruby 文件，文件中包含的组件类与文件同名：

`app/components/test_component.rb`:



```
class TestComponent < ViewComponent::Base
  def initialize(title:)
    @title = title
  end
end
```



`app/components/test_component.html.erb`:



```
<span title="<%= @title %>"><%= content %></span>
```



视图被渲染如下：



```
<%= render(TestComponent.new(title: "my title")) do %>
  Hello, World!
<% end %>
```



返回：

```
<span title="my title">Hello, World!</span>
```



##### 内容



内容被当作一个被捕获的代码块，传递给 ViewComponent，并赋予访问权限。

View Component 可以定义额外的内容区域。举一个例子：

```
app/components/modal_component.rb:
```

```
class ModalComponent < ViewComponent::Base
  with_content_areas :header, :body
end
```

`app/components/modal_component.html.erb`：

```
<div class="modal">
  <div class="header"><%= header %></div>
  <div class="body"><%= body %></div>
</div>
```

渲染视图：

```
<%= render(ModalComponent.new) do |component| %>
  <% component.with(:header) do %>
    Hello Jane
  <% end %>
  <% component.with(:body) do %>
    <p>Have a great day.</p>
  <% end %>
<% end %>
```

返回：

```
<div class="modal">
  <div class="header">Hello Jane</div>
  <div class="body"><p>Have a great day.</p></div>
</div>
```



##### 插槽（实验性质的）



插槽当前还在开发中，她是用于访问内容区域的。`Slot APIs` 应被视为未完成，以及将会发生重大改变。



插槽允许多个块传递到一个 ViewComponent，从而降低了复杂组件的复杂度。



插槽通过 `renders_one` 和 `renders_many` 来定义。



`renders_one` 定义一个在每个组件中，最多可以渲染一次的插槽：`renders_one :header`

`renders_many` 定义一个在每个组件中都可以渲染多次的插槽：`renders_may :blog_posts`



##### 定义插槽



插槽有三种形式：

- [Delegate slots](https://viewcomponent.org/#delegate-slots) 渲染其他组件。
- [Lambda slots](https://viewcomponent.org/#lambda-slots) 渲染为字符串或其他组件。
- [Pass through slots](https://viewcomponent.org/#pass-through-slots) 将内容直接传递给其他组件。



##### 代理组件



代理组件代理为其他组件：

`# blog_component.rb`

```
class BlogComponent < ViewComponent::Base
  include ViewComponent::SlotableV2

  # Since `HeaderComponent` is nested inside of this component, we have to
  # reference it as a string instead of a class name.
  renders_one :header, "HeaderComponent"

  # `PostComponent` is defined in another file, so we can refer to it by class name.
  renders_many :posts, PostComponent

  class HeaderComponent < ViewComponent::Base
    attr_reader :title

    def initialize(title:)
    end
  end
end
```



`# blog_component.html.erb`

```

<div>
  <h1><%= header %></h1> <!-- render the header component -->

  <% posts.each do |post| %>
    <div class="blog-post-wrapper">
      <%= post %> <!-- render an individual post -->
    </div>
  <% end %>
</div>
# index.html.erb
<%= render BlogComponent.new do |c| %>
  <% c.header do %>
    <%= link_to "My Site", root_path %>
  <% end %>

  <%= c.post(title: "My blog post") do %>
    Really interesting stuff.
  <% end %>

  <%= c.post(title: "Another post!") do %>
    Blog every day.
  <% end %>
<% end %>
```



##### Lambda 插槽



Lambda 插槽渲染表达式的返回值。Lambda 插槽被用于与`content_tag` 一同工作，或者作为具有特定默认值的另一个组件的封装。



```
class Blogcomponent < ViewComponent::Base
  include ViewComponent::SlotableV2

  # Renders the returned string
  renders_one :header, -> (title:) do
    content_tag :h1 do
      link_to title, root_path
    end
  end

  # Returns a component that will be rendered in that slot with a default argument.
  renders_many :posts, -> (title:, classes:) do
    PostComponent.new(title: title, classes: "my-default-class " + classes)
  end
end
```



##### 直通插槽



直通插槽捕获通过块传递的内容。

通过省略 `renders_one` 和 `renders_many` 的第二个参数，来定义直通插槽：

```
# blog_component.rb
class BlogComponent < ViewComponent::Base
  include ViewComponent::SlotableV2

  renders_one :header
  renders_many :posts
end
```



`#blog_component.html.erb `

```
<div>
  <h1><%= header %></h1>

  <%= posts %>
</div>
```



`# index.html.erb`



```
<div>
  <%= render BlogComponent.new do |c| %>
    <%= c.header do %>
      <%= link_to "My blog", root_path %>
    <% end %>

    <% @posts.each do |post| %>
      <%= c.post(post: post) %>
    <% end %>
  <% end %>
</div>
```



##### 渲染集合

几何插槽（通过 `renders_may` 定义）也可以传递一个集合。



`# navigation_component.rb`



```
class NavigationComponent < ViewComponent::Base
  include ViewComponent::SlotableV2

  renders_many :links, "LinkComponent"

  class LinkComponent < ViewComponent::Base
    def initialize(name:, href:)
      @name = name
      @href = href
    end
  end
end
```

```
# navigation_component.html.erb
```

```
<div>
  <% links.each do |link| %>
    <%= link %>
  <% end %>
</div>
```

```
# index.html.erb
```

```
<%= render(NavigationComponent.new) do |c| %>
  <%= c.links([
    { name: "Home", href: "/" },
    { name: "Pricing", href: "/pricing" },
    { name: "Sign Up", href: "/sign-up" },
  ]) %>
<% end %>
```



#### 内联组件



ViewComponent 可以不依赖模板文件进行渲染，只定义一个 `call` 方法：

`app/components/inline_component.rb`:

```
class InlineComponent < ViewComponent::Base
  def call
    if active?
      link_to "Cancel integration", integration_path, method: :delete
    else
      link_to "Integrate now!", integration_path
    end
  end
end
```

可以为不同种类的产品定义方法：

```
class InlineVariantComponent < ViewComponent::Base
  def call_phone
    link_to "Phone", phone_path
  end

  def call
    link_to "Default", default_path
  end
end
```

然后通过 `with_variant` 进行渲染：

```
<%= render InlineVariantComponent.new.with_variant(:phone) %>

# output: <%= link_to "Phone", phone_path %>
```



#### 模板继承



如果组件继承自其他组件，那么也会继承其的模板。如果自定义模板，则会覆盖。



```
# If `my_link_component.html.erb` is not defined the component will fall back
# to `LinkComponent`s template
class MyLinkComponent < LinkComponent
end
```



#### Sidecar 资源



ViewComponent 支持两种方式定义其视图文件。

##### Sidecar view

最简单的方式是定义在保存组件的 Ruby 文件后面：

```
app/components
├── ...
├── test_component.rb
├── test_component.html.erb
├── ...
```

##### Sidecar 目录

另一种方式，视图和其资源文件，可以保存在与组件同名的文件中，目录中的资源文件，如 JavaScript 和 CSS 文件将会被使用。

```
app/components
├── ...
├── example_component.rb
├── example_component
|   ├── example_component.css
|   ├── example_component.html.erb
|   └── example_component.js
├── ...
```

可以使用`--sidecar` 参数，来通过命令生成 sidecar 目录：

```
bin/rails generate component Example title content --sidecar
      invoke  test_unit
      create  test/components/example_component_test.rb
      create  app/components/example_component.rb
      create  app/components/example_component/example_component.html.erb
```



