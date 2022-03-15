翻译自：[Building a Component Library in Rails With Storybook - DEV Community](https://dev.to/orbit/building-a-component-library-in-rails-with-storybook-49m4)



最近几年，Rails 生态系统突飞猛进的得到进化，并且得到了使用 JavaScript 框架的开发人员的喜爱。



在代号为 NEW MAGIC（现在被称为  [Hotwire](http://hotwire.dev/)）的背后，Basecamp 团队在 2020 年，发布了  [Turbo](https://turbo.hotwire.dev/)  和 [Stimulus](https://stimulus.hotwire.dev/)，添加强大的功能，如近即时导航、第一方 WebSocket 支持、应用程序的延迟加载，以及许多其他功能。



然而，我最感兴趣的使用 Rails 开发的部分，是构建自己的组件库的能力，由 [View Component](https://viewcomponent.org/) 和 [Storybook](https://storybook.js.org/) 提供支持。



组件库是一组可以在整个应用中重用的组件（buttons、alert、特定领域的小部件等），减少了重复的构建，并改善了我们的用户体验和代码库的一致性。



本文将介绍如何构建你自己的视图组件库，并将其与Storybook一起部署，团队的所有成员能够单独尝试、调整和检查它们。



#### ViewComponents 和 Storyboard 入门



去年秋天，我偶然发现了 Joel Hawksley在 RailsConf上 发表的一个名为“ [Encapsulating Views](https://railsconf.org/2020/2020/video/joel-hawksley-encapsulating-views)”的演讲，介绍了视图组件gem GitHub如何实现 Rails 中的类似 react 的组件。



视图组件使得在你的Ruby on Rails应用程序中构建可重用、可测试和封装的组件变得很容易。我强烈建议在继续解释文档的好处和用例之前，先看一看文档的前几段。



在Orbit，我们正在慢慢地构建一个视图组件列表，我们可以在应用程序的按钮、选择、下拉菜单中重用这些组件。然而，随着列表的增长，整个团队(工程、设计和产品)越来越难知道哪些组件已经可用，哪些组件可以重用。我们需要一种方法来组织这个库。



在基于js的应用程序中，有一个常见的(而且真的很神奇的)组件库工具叫做 [Storybook](https://storybook.js.org/)。Storybook 是一个通过响应式的操作，来构建 UI 组件，以及组件的文档和其他细节。这里有一些 Storybook 的例子：

- 这个例子来自 [The Guardian](https://5dfcbf3012392c0020e7140b-gmgigeoguh.chromatic.com/?path=/story/layouts-immersive--article-story)
- 这个例子来自 [GitLab](https://gitlab-org.gitlab.io/gitlab-ui/?path=/story/base-broadcast-message--default)
- 这个例子来自 [Shopify](https://5d559397bae39100201eedc1-nqqiwjtuqe.chromatic.com/?path=/story/all-components-skeleton-page--all-examples)
- 还有我们自己 [Orbit](https://app.orbit.love/_storybook/index.html)



Storybook 只用于使用 JavaScript 框架创建的单页面 Web 应用：React，Vue，Angular，以及其他一些。幸运的是，最新的 V6 版本，引入了 [@storybook/server](https://github.com/storybookjs/storybook/tree/master/app/server) 包，允许任意的 HTML 代码使用 Storybook 组件。理论上，这允许 Rails 后端呈现 Storybook 的组件。但这在实践中是如何运作的呢？



本文将从新一个全新的 Rails 项目入手，安装所有必须的 gems，创建我们的第一个 ViewComponent，然后在 Storyboard 中显示它，并将它部署到我们的应用程序中。该项目的源码已经放在了 GitHub 上：https://github.com/phacks/rails-view-components-storybook。



如果你想跳到文章中某个特定的部分（因为您可能已经很熟悉我们将要讨论的一些概念），这是本文其余部分的大纲：

- 初始化一个 Rails 项目
- 创建我们第一个 View Component
- 设置可以预览的组件
- 通过 `ViewComponent::Storybook` gem，设置 Storybook
- 写一个 Story，给我们的 ButtonComponent
- 将我们的 Storybook，部署到我们的 app 中
- 结论



#### 初始化一个 Rails 项目



让我们创建一个新的 Rails 项目，一步一步跟着 [Rails 新手指南](https://guides.rubyonrails.org/getting_started.html) 的 3.1 节来做，然后运行



```
rails new rails-view-components-storybook
cd rails-view-components-storybook
rails webpacker:install

# in one terminal window
bin/webpack-dev-server

# in another terminal window
rails server
```



此时项目启动起来了，并运行在 [http://localhost:3000](http://localhost:3000/)。



我们将在Rails应用程序中添加一个静态页面，它将作为一个厨房水槽来查看并与我们即将到来的视图组件交互。为此，我们可以创建或更新以下文件：



```
app/controllers/pages_controller.rb
```



```
class PagesController < ApplicationController
  def show
    render template: "pages/#{params[:page]}"
  end
end
```



```
config/routes.rb
```



```
Rails.application.routes.draw do
  get "/pages/:page" => "pages#show"
end
```



```
app/views/pages/kitchen-sink.html.erb
```



```
<article class="prose m-24">
  <h1>ViewComponents kitchen sink</h1>
  <p>This page will demo our ViewComponents</p>
</article>
```



我们现在应该看到新页面：http://localhost:3000/pages/kitchen-sink。



为了给我们即将到来的组件添加样式，我们将添加TailwindCSS(一个实用程序优先的CSS框架)。请注意，这不是Storybook或ViewComponents的要求，我们在这里安装它只是为了简洁和方便我们的组件样式。继续阅读本文，您不需要具备任何关于 Tailwind 的先验知识。



替换 `app/views/layouts/application.html.erb` 中的内容：



```
<!DOCTYPE html>
<html>
  <head>
    <title>RailsViewComponentsStorybook</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
    <link rel="stylesheet" href="https://unpkg.com/tailwindcss@^2/dist/base.min.css" />
    <link rel="stylesheet" href="https://unpkg.com/tailwindcss@^2/dist/components.min.css" />
    <link
      rel="stylesheet"
      href="https://unpkg.com/@tailwindcss/typography@0.2.x/dist/typography.min.css"
    />
    <link rel="stylesheet" href="https://unpkg.com/tailwindcss@^2/dist/utilities.min.css" />
  </head>

  <body>
    <%= yield %>
  </body>
</html>
```



注意:虽然使用unpkg是安装 TailwindCSS 最简单的方法，但不建议在生产应用程序中这样做，因为这会导致性能问题。如果您想要为生产应用程序安装 TailwindCSS，我建议您按照它们的说明进行安装： [their instructions](https://tailwindcss.com/docs/installation#installing-tailwind-css-as-a-post-css-plugin)。



#### 创建我们第一个 View Component



在web应用程序中，按钮是最常用的UI组件之一，通常是创建组件库时首先想到的组件之一。让我们构建一个Button ViewComponent。



在 Gemfile 中，添加：

```
 gem "view_component", require: "view_component/engine"
```



然后运行 `bundle install`，然后重启 Rails 服务，来加载 ViewComponent gem。



我们希望我们的按钮有不同的风格，取决于我们如何计划使用它：`primary`，`outline` 和 `danger`。让我们创建一个名为 Button 的新ViewComponent，它带有一个 type 属性：



```
# in another terminal window
bin/rails generate component Button type --preview
```



这个命令生成四个文件：

- `app/components/button_component.rb`：ViewComponent 本体；
- `app/components/button_component.html.erb`：ViewComponent 模板；
- `test/components/button_component_test.rb`：ViewComponent 测试；
- `test/components/previews/button_component_preview.rb`：ViewComponent 预览；



我们不打算在这篇文章中讨论ViewComponents测试;如果你很好奇，相关 [文档](https://viewcomponent.org/guide/testing.html) 页面是一个很好的入门资源。



让我们定义我们的组件模板，以便它输出一个样式化的 `<button>` 来呈现传递到ViewComponent中的 `<content>`：



```
app/components/button_component.html.erb
```



```
<button class="<%= classes %>">
  <%= content %>
</button>
```



然后，我们可以向不同的类添加不同的逻辑：

```
app/components/button_component.rb
```

```
# frozen_string_literal: true

class ButtonComponent < ViewComponent::Base
  attr_accessor :type

  PRIMARY_CLASSES = %w[
    disabled:bg-purple-300
    focus:bg-purple-600
    hover:bg-purple-600
    bg-purple-500
    text-white
  ].freeze
  OUTLINE_CLASSES = %w[
    hover:bg-gray-200
    focus:bg-gray-200
    disabled:bg-gray-100
    bg-white
    border
    border-purple-600
    text-purple-600
  ].freeze
  DANGER_CLASSES = %w[
    hover:bg-red-600
    focus:bg-red-600
    disabled:bg-red-300
    bg-red-500
    text-white
  ].freeze
  BASE_CLASSES = %w[
    cursor-pointer
    rounded
    transition
    duration-200
    text-center
    p-4
    whitespace-nowrap
    font-bold
  ].freeze

  BUTTON_TYPE_MAPPINGS = {
    primary: PRIMARY_CLASSES,
    danger: DANGER_CLASSES,
    outline: OUTLINE_CLASSES
  }.freeze

  def initialize(type: :primary)
    @type = type
  end

  def classes
    (BUTTON_TYPE_MAPPINGS[@type] + BASE_CLASSES).join(' ')
  end

end
```



最终，我们可以在 kitchen sink 页面，实例化所有这三种类型的按钮：



```
app/views/pages/kitchen-sink.html.erb
```



```
<article class="prose m-24">
  <h1>ViewComponents kitchen sink</h1>
  <p>This page will demo our ViewComponents</p>

  <h2>ButtonComponent</h2>
  <h3>Primary</h3>
  <%= render(ButtonComponent.new(type: :primary)) do %>
    Submit
  <% end %>

  <h3>Outline</h3>
  <%= render(ButtonComponent.new(type: :outline)) do %>
    Cancel 
  <% end %>

  <h3>Danger</h3>
  <%= render(ButtonComponent.new(type: :danger)) do %>
    Delete
  <% end %>
</article>
```



此时，我们的 `ButtonComponent` 组件，已经准备好使用。



![The Kitchen Sink page now displays three button: one is styled with the primary color, another is outline, and the third is red](https://res.cloudinary.com/practicaldev/image/fetch/s--31Ir3QXK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.sanity.io/images/cad8jutx/production/5f8f06192f54225b74adcac489ab22cf47eb4e8c-1374x1334.png%3Fw%3D992%26h%3D963)

#### 设置组件预览



ViewComponents 提供了一个方便的特性：组件预览。它们允许我们获得一个URL，在这个URL中，我们可以单独查看 ViewComponent 组件，并与之交互。



我们可以预览 `ButtonComponent` 组件，通过下面的 URL：

http://localhost:3000/rails/view_components/button_component/default



默认预览，不带任何参数实例化 ButtonComponent，这解释了为什么我们看到 :primary 按钮类型而没有内容。我们可以更新预览文件，让它可以预览不同的变体：



```
test/components/previews/button_component_preview.rb
```



```
class ButtonComponentPreview < ViewComponent::Preview
  def default(type: :primary)
    type = type.to_sym if type

    render(ButtonComponent.new(type: type)) { 'Button' }
  end
end
```



我们可以通过 `type` 和 `content` 查询参数，来控制组件的展示。比如：

http://localhost:3000/rails/view_components/button_component/default?type=danger 将生成一个红色的按钮，而 http://localhost:3000/rails/view_components/button_component/default?type=outline  将生成一个线框按钮。



让我们为每个按钮，不同状态下，添加单独的反应。这使得当组件在受支持状态下增长时，很容易推理，因为它减少了关于哪些道具打算一起使用的模糊性：



```
test/components/previews/button_component_preview.rb
```



```
class ButtonComponentPreview < ViewComponent::Preview
  def default(type: :primary)
    type = type.to_sym if type

    render(ButtonComponent.new(type: type)) { 'Button' }
  end

  def primary
    render(ButtonComponent.new(type: :primary)) { 'Submit' }
  end

  def outline
    render(ButtonComponent.new(type: :outline)) { 'Cancel' }
  end

  def danger
    render(ButtonComponent.new(type: :danger)) { 'Delete' }
  end
end
```



我们可以通过以下链接来预览上面不同状态的按钮：

- http://localhost:3000/rails/view_components/button_component/primary
- http://localhost:3000/rails/view_components/button_component/outline
- http://localhost:3000/rails/view_components/button_component/danger



我们将在下一节，通过 Storybook 来控制 ViewComponent。是时候将 Storybook 添加到我们的项目中了。



#### 通过 `ViewComponent::Storybook` Gem，来设置 Storybook



[view_component_storybook](https://github.com/jonspalmer/view_component_storybook) gem，用于链接 Ruby on Rails 和 Storybook。使用它，我们可以通过 Ruby DSL 来编写 Stories（Storybook 的主要概念：考虑 UI 组件的特定状态） ，然后将 Storybook  翻译过来。它还负责将 ViewComponents 预览和Storybook 的API粘在一起。



重要提示：下面的说明不同于视图组件storybook的官方文档。这个版本允许更容易地将Storybook部署到一个公共URL，这将在将我们的Storybook部署到我们的应用程序中进行讨论。如果你不打算部署你的Storybook，你可能想按照官方文档来代替。



首先，在控制台中，我们可以安装以下Storybook包。这是使Storybook界面启动和运行所必需的：



```
yarn add @storybook/server @storybook/addon-controls --dev
```



然后，让我们将`view_component_storybook` gem 添加到 Gemfile 中，并在应用程序中声明它：



`Gemfile`



```
gem "view_component_storybook"
```



`config/application.rb`



```
require_relative "boot"

require "rails/all"

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module RailsViewComponentsStorybook
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 6.1

    # Configuration for the application, engines, and railties goes here.
    #
    # These settings can be overridden in specific environments using the files
    # in config/environments, which are processed later.
    #
    # config.time_zone = "Central Time (US & Canada)"
    # config.eager_load_paths << Rails.root.join("extras")

    require "view_component/storybook/engine"

    # Enable ViewComponents previews
    config.view_component.show_previews = true
  end
end
```



然后，我们可以在位于项目根目录的一个新的 Storybook 文件夹中创建Storybook配置文件：



```
.storybook/main.js
```

```
module.exports = {
  stories: ["../test/components/**/*.stories.json"],
  addons: ["@storybook/addon-controls"],
};
```

```
.storybook/preview.js
```

```
export const parameters = {
  server: {
    url: `${location.protocol}${location.hostname}${
      location.port !== "" ? ":3000" : ""
    }/rails/view_components`,
  },
};
```



我们将通过在 package 中添加快捷方式来结束设置。json来构建 Storybook 文件：

`package.json`

```
 {
  "name": "rails-view-components-storybook",
  // ...
  "scripts": {
    "storybook:build": "build-storybook -o public/_storybook"
  }
}
```



然后，我们可以重新启动Rails服务器来记录新的gem。



这要配置许多东西，幸运的是，我们只需要一次设置所有的东西。我们现在应该都准备好了。让我们通过运行来检查Storybook是否正确设置：`yarn storybook:build`，然后访问 `http://localhost:3000/_storybook/index.html`。



![The Storybook instance is running, but says: “Oh no! Your Storybook is empty.”](https://res.cloudinary.com/practicaldev/image/fetch/s--4bLPsGWJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.sanity.io/images/cad8jutx/production/ebda4d4651cdc12ddd19e330f7fa540a8977b7a9-2762x952.png%3Frect%3D0%2C1%2C2762%2C949%26w%3D992%26h%3D341)



当我们启动并运行 Storybook 时，你可能会注意到我们的 ButtonComponent 不见了。这很正常:我们需要先为它写一个 `Story`。



#### 为我们的 Button Component 写一个 Story



在Storybook中，一个故事代表一个UI组件的状态。一个组件可以有一个或多个故事，这通常取决于其复杂性:可以想象Select组件有几个选项，或者很多，或者根本没有选项。在我们的例子中，我们将为Button组件(:primary，:outline和:danger)的每个状态创建一个故事，以及另一个默认的故事，它将允许我们以交互方式控制类型。



Story 还可以定义一个或多个控件：这些控件将定义组件的交互部分。在我们的默认故事中，我们可以为按钮类型定义一个控件。该控件将是一个选择，因为我们希望Storybook访问者能够在三个可用选项之间选择类型。在视图组件storybook gem中有更多的控件可用，完整的列表在 [这里](https://github.com/jonspalmer/view_component_storybook/blob/main/lib/view_component/storybook/dsl/controls_dsl.rb)。



让我们为我们的组件，创建一个 Story，使用 Story DSL `view_component_storybook`：



`test/components/stories/button_component_stories.rb`



```
class ButtonComponentStories < ViewComponent::Storybook::Stories
  story(:default) do
    controls do
      select(:type, %w[primary outline danger], 'primary')
    end
  end

  story(:primary) {}
  story(:outline) {}
  story(:danger) {}
end
```



我们现在可以要求 `view_component_storybook` 将 Ruby Story 转换为 JSON 版，然后 storybook 会自动获取这个故事：



```
rake view_component_storybook:write_stories_json
```



这生成一个新的 `button_component.stories.json` 文件，还有与 Storybook 的 API 兼容的 story。



让我们重新构建我们的 Storybook 实例，以查看该 Story 的运行情况。



现在，http://localhost:3000/_storybook/index.html 将显示不同状态下，我们的按钮，关联控件，交互式的更改默认的 Story 类型。



![A GIF navigating the Button stories in Storybook. It clicks through the stories for the primary, outlined, and danger buttons, and then a “default” one which changes the type when the appropriate control gets selected](https://res.cloudinary.com/practicaldev/image/fetch/s--FGByCqjf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.sanity.io/images/cad8jutx/production/78130106c956fe4c508b6ad77f0c1370a7ef7066-1200x654.gif%3Frect%3D0%2C0%2C1200%2C653%26w%3D992%26h%3D540)



恭喜，我们已经在Rails组件库中创建了第一个组件



#### 在我们的应用程序旁，部署 Storybook



当整个团队的工程师、设计人员和产品人员都能看到哪些组件可用，知道哪些变体和定制选项可用，并通过直接使用它们来了解如何使用它们时，组件库的工作效果最好。一个公开可访问的URL是实现这一点的好方法，因为当讨论一个即将到来的特性时，可以包含一个到特定组件变体的链接。





















































