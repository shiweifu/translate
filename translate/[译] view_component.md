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



##### Sidecar 目录中的组件文件



另一个可能保存 Ruby 组件文件的位置是 sidecar 目录，将所有相关的文件放在一个文件夹中。



>  注意：不要将包含的文件夹和 .rb 文件保存为相同的名称，否则模块定义和类定义之间将会发生冲突。

````
app/components
├── ...
├── example
|   ├── component.rb
|   ├── component.css
|   ├── component.html.erb
|   └── component.js
├── ...
````



组件可以使用与文件夹相同的名字作为命名空间，进行渲染：

```
<%= render(Example::Component.new(title: "my title")) do %>
  Hello, World!
<% end %>
```

##### 条件渲染

组件可以实现 `#render?` 方法，在组件初始化后，通过调用这个方法，来判断是否渲染组件。

传统上，是否渲染视图的逻辑，放在两个组件模板中：

`app/components/confirm_email_component.html.erb`

```
<% if user.requires_confirmation? %>
  <div class="alert">Please confirm your email address.</div>
<% end %>
```

或者呈现组件的视图：

`app/views/_banners.html.erb`

```
<% if current_user.requires_confirmation? %>
  <%= render(ConfirmEmailComponent.new(user: current_user)) %>
<% end %>
```



通过调用 `ViewComponent::TestHelpers` 模块中的 `refute_component_rendered` 方法，可以验证组件是否已经渲染。



##### before_render



组件可以定义 `before_render` 方法，该方法在组件渲染之前被调用，`herlpers` 允许在此时被调用：

`app/components/confirm_email_component.rb`



```
class MyComponent < ViewComponent::Base
  def before_render
    @my_icon = helpers.star_icon
  end
end
```



##### 渲染合集

使用 `with_collection` 来渲染一组 `ViewComponent`：

`app/view/products/index.html.erb`

```
<%= render(ProductComponent.with_collection(@products)) %>
```

```
app/components/product_component.rb
class ProductComponent < ViewComponent::Base
  def initialize(product:)
    @product = product
  end
end
```

[默认情况下](https://github.com/github/view_component/blob/89f8fab4609c1ef2467cf434d283864b3c754473/lib/view_component/base.rb#L249)，组件的名称被用于定义从集合传递给组件的参数。

##### with_collection_parameter

使用 `with_collection_parameter` 改变集合参数的名称：

`app/components/product_component.rb`

```
class ProductComponent < ViewComponent::Base
  with_collection_parameter :item

  def initialize(item:)
    @item = item
  end
end
```



##### 额外参数

额外参数用于遍历集合时，传递给每个组件实例：



```
app/view/products/index.html.erb
```



```
<%= render(ProductComponent.with_collection(@products, notice: "hi")) %>
```



```
app/components/product_component.rb
```



```
class ProductComponent < ViewComponent::Base
  with_collection_parameter :item

  def initialize(item:, notice:)
    @item = item
    @notice = notice
  end
end
```



```
app/components/product_component.html.erb
```



```
<li>
  <h2><%= @item.name %></h2>
  <span><%= @notice %></span>
</li>
```



#### Collection counter



ViewComponent 在匹配参数名字的时候，定义了一个计数器变量，以 `_counter` 结尾。要访问这个变量，在 `initialize` 以参数的形式接受：

`app/components/product_component.rb`

```
class ProductComponent < ViewComponent::Base
  def initialize(product:, product_counter:)
    @product = product
    @counter = product_counter
  end
end
```

`app/components/product_component.html.erb`

```
<li>
  <%= @counter %> <%= @product.name %>
</li>
```



#### 使用帮助函数

帮助函数通过 `helpers` 代理进行使用：

```
module IconHelper
  def icon(name)
    tag.i data: { feather: name.to_s.dasherize }
  end
end

class UserComponent < ViewComponent::Base
  def profile_icon
    helpers.icon :user
  end
end
```

它可以被用于代理：

```
class UserComponent < ViewComponent::Base
  delegate :icon, to: :helpers

  def profile_icon
    icon :user
  end
end
```

帮助同样可以被引用：

```
class UserComponent < ViewComponent::Base
  include IconHelper

  def profile_icon
    icon :user
  end
end
```



#### 编写测试



组件可以直接进行单元测试，使用 `render_inline` 测试方法，通过断言渲染输出结果。

如果 `Capybara` 的 gem 已经安装，可以直接使用：

```
require "view_component/test_case"

class MyComponentTest < ViewComponent::TestCase
  def test_render_component
    render_inline(TestComponent.new(title: "my title")) { "Hello, World!" }

    assert_selector("span[title='my title']", text: "Hello, World!")
    # or, to just assert against the text:
    assert_text("Hello, World!")
  end
end
```

如果 `capybara` 没有安装，断言通过 `render_inline` 实现，返回值为 `Nokogiri::HTML::DocumentFragment` 的实例。

```
def test_render_component
  result = render_inline(TestComponent.new(title: "my title")) { "Hello, World!" }

  assert_includes result.css("span[title='my title']").to_html, "Hello, World!"
end
```

另一种方案，对组件的原始输出进行断言，原始输出通过 `render_component` 暴露：

```
def test_render_component
  render_inline(TestComponent.new(title: "my title")) { "Hello, World!" }

  assert_includes rendered_component, "Hello, World!"
end
```

 使用 `with_content_areas` 进行测试：

```
def test_renders_content_areas_template_with_content
  render_inline(ContentAreasComponent.new(footer: "Bye!")) do |component|
    component.with(:title, "Hello!")
    component.with(:body) { "Have a nice day." }
  end

  assert_selector(".title", text: "Hello!")
  assert_selector(".body", text: "Have a nice day.")
  assert_selector(".footer", text: "Bye!")
end
```



##### Action Pack Variants

使用 `with_variant` 帮助方法来测试指定的变体：

```
def test_render_component_for_tablet
  with_variant :tablet do
    render_inline(TestComponent.new(title: "my title")) { "Hello, tablets!" }

    assert_selector("span[title='my title']", text: "Hello, tablets!")
  end
end

```



#### 预览组件



`ViewComponent::Preview`，与 `ActionMailer::Preview` 类似，提供一种在隔离状态下的方式来预览组件：

`test/components/previews/test_component_preview.rb`

```
class TestComponentPreview < ViewComponent::Preview
  def with_default_title
    render(TestComponent.new(title: "Test component default"))
  end

  def with_long_title
    render(TestComponent.new(title: "This is a really long title to see how the component renders this"))
  end

  def with_content_block
    render(TestComponent.new(title: "This component accepts a block of content")) do
      tag.div do
        content_tag(:span, "Hello")
      end
    end
  end
end
```

这生成了 http://localhost:3000/rails/view_components/test_component/with_default_title，http://localhost:3000/rails/view_components/test_component/with_long_title， 和 http://localhost:3000/rails/view_components/test_component/with_content_block。



同样可以使用从获取动态变量，并传递为参数：

`test/components/previews/test_component_preview.rb`

```
class TestComponentPreview < ViewComponent::Preview
  def with_dynamic_title(title: "Test component default")
    render(TestComponent.new(title: title))
  end
end
```

允许以这种方式传递值： http://localhost:3000/rails/components/test_component/with_dynamic_title?title=Custom+title。



`ViewComponent::Preview` 的基类引入了[`ActionView::Helpers::TagHelper`](https://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html)，该类提供了  [`tag`](https://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html#method-i-tag) 和 [`content_tag`](https://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html#method-i-content_tag) 视图帮助方法。



之前的应用使用了默认的 application 布局，但可以通过 `layout` 选项来指定一个布局：

`test/components/previews/test_component_preview.rb`

```
class TestComponentPreview < ViewComponent::Preview
  layout "admin"

  ...
end
```

你也可以通过 `default_preview_layout` 配置选项，来设置默认的的布局文件：

`config/application.rb`

```
# Set the default layout to app/views/layouts/component_preview.html.erb
config.view_component.default_preview_layout = "component_preview"
```

刚刚创建的类保存在 `test/components/previews`，地址通过 `preview_paths` 选项来指定：

`config/application.rb`

```
config.view_component.preview_paths << "#{Rails.root}/lib/component_previews"
```

默认情况下，预览地址为 http://localhost:3000/rails/view_components. 如果需要使用不同的地址，通过 `preview_route` 选项来指定：

```
config/application.rb
```

```
config.view_component.preview_route = "/previews"
```

此时，预览地址为：http://localhost:3000/previews。



#### 预览模板

预览保存在 `test/components/previews/cell_component_preview/` 的文件时， 它的模板文件可以保存在：

`test/components/previews/cell_component_preview.rb`



```
class CellComponentPreview < ViewComponent::Preview
  def default
  end
end
```



`test/components/previews/cell_component_preview/default.html.erb`



```
<table class="table">
  <tbody>
    <tr>
      <%= render CellComponent.new %>
    </tr>
  </tbody>
</div>
```



预览不同位置的文件时，传递 `template` 参数：该参数在 `config/view_component.preview_path`：

`test/components/previews/cell_component_preview.rb`

```
class CellComponentPreview < ViewComponent::Preview
  def default
    render_with_template(template: 'custom_cell_component_preview/my_preview_template')
  end
end
```

通过 `params` 参数传递过来的值，可以通过 `locals` 来进行访问：

`test/components/previews/cell_component_preview.rb`

```
class CellComponentPreview < ViewComponent::Preview
  def default(title: "Default title", subtitle: "A subtitle")
    render_with_template(locals: {
      title: title,
      subtitle: subtitle
    })
  end
end
```

允许传值通过URL：http://localhost:3000/rails/components/cell_component/default?title=Custom+title&subtitle=Another+subtitle。



#### 配置预览控制器

模板的预览可以被扩展，比如增加权限验证等需要在实际掉用之前被执行的方法，通过 `preview_controller` 的选项来进行配置：

`config/application.rb`

```
config.view_component.preview_controller = "MyPreviewController"
```



##### 配置测试控制器



组件测试时假设存在 `ApplicationController` 类，可以使用 `test_controller` 选项对其进行测试：



`config/application.rb`

```
config.view_component.test_controller = "BaseController"
```



#### 设置RSpec



要使用 RSpec，添加以下语句：

```
spec/rails_helper.rb
```



```
require "view_component/test_helpers"

RSpec.configure do |config|
  config.include ViewComponent::TestHelpers, type: :component
end
```



Specs 通过访问帮助方法 `render_inline` 来生成。

使用组件预览：

```
config/application.rb
```

```
config.view_component.preview_paths << "#{Rails.root}/spec/components/previews"
```



#### 使渲染的 monkey patch（Rails<6.1）无效



为了避免 `ViewComponent` 和其他 `monkey patch` `render` 方法互相覆盖，可以将 `ViewComponent` 配置为不引入 `monkey patch`：



```
config.view_component.render_monkey_patch_enabled = false # defaults to true
```



在禁用 `monkey patch` 的情况下，请改用 `render_component`（或者 render_component_to_string）：



```
<%= render_component Component.new(message: "bar") %>
```



#### Sidecar assets （实验）



可以在组件（有时称为 `sidecar` 资源或文件）旁边添加 `JavaScript` 和 `CSS`。



使用 Webpacker gem 变异 sidecar 资源时，将文件放在 `app/component` 目录：

1. 在 `config/webpacker.yml`，增加 `app/components` 到 `resloved_array` 数组（比如： `resolved_paths: ["app/components"]`）。
2. In the Webpack entry file (often `app/javascript/packs/application.js`), add an import statement to a helper file, and in the helper file, import the components’ Javascript：



```
import "..components"
```



然后，在`app/javascript/component.js` 文件，添加：



```
function importAll(r) {
  r.keys().forEach(r)
}

importAll(require.context("../components", true, /_component.js$/))
```



任何以`_component.js` 为后缀的文件，将被打包成 `Webpack`  的文件，举个例子：`app/component/widge_coomponent.css`。如果 Webpack 



任何带有_component.js后缀的文件（例如app / components / widget_component.js）都将被编译到Webpack捆绑包中。 如果该文件本身导入了另一个文件（例如app / components / widget_component.css），则在将Webpack用于样式的情况下，该文件也将被编译并捆绑到Webpack的输出样式表中。





