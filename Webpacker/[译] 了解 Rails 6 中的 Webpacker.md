本文介绍了 Webpacker 的一些概念，以及在 Rails 6 中的基本应用。

翻译自：[https://prathamesh.tech/2019/08/26/understanding-webpacker-in-rails-6/](https://prathamesh.tech/2019/08/26/understanding-webpacker-in-rails-6/)


从 Rails 6 开始，Webpacker 是默认的 JavaScript 编译器。之前使用的资源处理工具 Sprockets 将全面被 Webpacker 替代。从此项目中的 JavaScript 代码都将被 Webpacker 所管理。Webpacker 的设计理念和实现，都不同于资源管理工具。在本篇日志中，我们将了解到 Webpacker 是如何管理 JavaScript 的。

### 什么是Webpacker 

`webpack` 是一个流行的 JavaScript 打包工具，`webpacker` 将其封装成一个`Gem`包，额外提供了一些方便在 Rails 中使用 webpack 的工具。可以很容易的把 Rails 和 webpack 集成到一起。虽然这么描述可能有点简单，它是一个非常强大的工具，不过现在了解这些就足够了。

*Webpacker 将 webpack  封装成一个 Ruby gem，以及方便在 Rails 中使用 Webpack。*

当你创建一个新的 Rails 6 App，终端将输出：

```
      rails  webpacker:install
RAILS_ENV=development environment is not defined in config/webpacker.yml, falling back to production environment
      create  config/webpacker.yml
Copying webpack core config
      create  config/webpack
      create  config/webpack/development.js
      create  config/webpack/environment.js
      create  config/webpack/production.js
      create  config/webpack/test.js
```

可以看到 `webpacker` 已经默认是在 `Gemfile` 中了。`rails new` 命令也会使用 `yarn` 安装一大堆 npm 包。

> 当旧的应用升级到 Rails 6，默认是没有引入 `webpacker` 包的。你需要手动将其引入到 Gemfile，然后运行 `rails webpacker:install`。之后的日志李，我们将介绍如何在旧项目中使用 `Webpacker`。

### JavaScript 代码新的位置

Rails 6以前，所有的 JavaScript 代码放在 `app/assets/javascripts`。在 Rails 6 中，`app/assets/javascripts` 已经不存在了。代替它的是 `app/javscript`，我们把 JavaScript 代码放置其中。此文件夹包含了应用中前端部分所有用到的JavaScript 文件。

让我们看看一个空的 Rails 6 应用程序这块的目录结构：

```
Projects/scratch/better_hn  master ✗ 2.6.3                                                        ◒
▶ tree app/javascript
app/javascript
├── channels
│   ├── consumer.js
│   └── index.js
└── packs
    └── application.js

2 directories, 3 files
```

`channels` 和 `packs` 包含在其中。`channels` 目录是由 Action Cable 组件生成的，我们现在可以忽略它。`packs` 目录是我们要重点关注的，让我们看看里面有什么。

```
// app/javascript/pack/application.js
require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")
```

### 什么是 Pack（翻译为`包`）？

webpack 有入口点的概念，第一次编译时，它会加载文件，寻找文件中的入口点，然后从入口点开始编译 JavaScript。Webpacker 在 `app/javascript/packs` 目录的 `application.js` 文件创建了 `application` pack。如果你记得 assets pipeline，这个文件就相当于 `app/assets/javascripts/application.js`。

`application` 包会生成调用 Rails 相关组件如`trubolinks`，`Active Storage`和`Action Cable` 的代码。

> 你也许注意到 Rails 框架使用的一些 JavaScript 组件如 Rails-UJS，Turbolinks，Active Storage 都已经支持  Webpacker。甚至 Rails 6 中引入的新组件如 Action Text，也都支持 Webpacker。

所以 `application` 包是所有 JavaScript 代码的入口。但是我们也可以创建自定义包，放置在 `app/javascript/packs` 目录，然后 Webpacker 将找到他们并编译。

可以在 `config/webpacker.yml` 文件中找到相关配置。

```
# config/webpacker.yml
5:  source_entry_path: packs
```

如果我们想让 webpack 添加额外需要打包的包含 JavaScript 代码目录，我们可以通过配置 `config/webpacker.yml` 文件中 `resolved_paths` 设置项来实现。这个文件中的设置项的意思都很一目了然。

### 编译 JavaScript 代码

下一步是看看如何使用 Webpacker 及 webpack 编译 JavaScript 代码。开发模式下，你不需要做任何事。当你运行 rails server 的时候，编译在请求时发生，工作方式和 assets pipeline 很类似。

#### 使用 webpack-dev-server 实现实时加载

`bin/webpack-dev-server` 是 Webpacker 生成的一个文件，用于在开发模式下的实时热加载。我们必须单独运行 `webpack-dev-server` 然后才能看到动态的重新加载和模块的热加载。

#### 生产模式

生产模式下，webpacker 增加了 `webpacker:compile` 任务到 `assets:precompile` 任务中。所以如果你运行 `assets:precompile` 任务，它将负责编译 webpack 处理后输出的代码。webpack 软件包是 `package.json` 的一部分，yarn 管理要处理的第三方 JavaScript 代码。

### 导入 JavaScript 代码到 App

我们已经看到了如何使用 webpacker 编译 JavaScript 代码，但是如何在我们的 App 中使用他们呢？

Webpacker 提供了辅助方法 `javascript_pack_tag`。就像名字说明的，我们在 layout 文件中使用它引入 Webpacker 打包后的代码。就像是 
使用 assets pipeline 时 `javascript_link_tag` 所做的那样。

```
# app/views/layouts/application.html.erb

<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
```

`javascript_pack_tag` 负责确保编译后的资源在开发模式和生产模式都可以被证确的引用。

-----

这就是本文的全部内容：我们概览了如何通过 Webpacker 在 Rails 6 项目中使用 webpack，我们了解 `包 (packs)` 这个概念，我们也看到了 Webpacker 的编译过程以及如何引入这些编译后的代码。

接下来，我们来聊聊 [在 Webpacker 中掌握"包"](https://prathamesh.tech/2019/09/24/mastering-packs-in-webpacker/)
