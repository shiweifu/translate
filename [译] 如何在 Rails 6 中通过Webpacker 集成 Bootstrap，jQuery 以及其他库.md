# 如何在 Rails 6 中通过Webpacker 集成 Bootstrap，jQuery 以及其他库

翻译自：[How to use Bootstrap, jQuery and other libraries in Rails 6 with Webpacker - Ruby Yagi 🐐](https://rubyyagi.com/how-to-use-bootstrap-and-jquery-in-rails-6-with-webpacker/)



在 Rails 6 中，Webpacker 替换了之前的资源打包工具，来处理 javascript 的编译和缩小。



Webpacker 是一个 gem，封装自 [webpack.js](https://webpack.js.org/)，webpack.js 处理打包 javascript 代码，webpacker 让我们可以通过一些接口，在 Rails 应用中，来使用 webpack。本文不探讨 webpacker 是如何工作的，我推荐 [Prathamesh’s article on understanding webpacker](https://prathamesh.tech/2019/08/26/understanding-webpacker-in-rails-6/)，这篇文章来了解 webpacker 是如何工作的。GoRails 同样有一些[很不错的 screencast](https://gorails.com/episodes/how-to-use-bootstrap-with-webpack-and-rails) 来讨论这个主题。



本文假设你创建一个新的 Rails 6 项目，想在其中集成Bootstrap。



## 安装和使用 Bootstrap 及 jQuery



首先打开终端，输入下面的命令，安装 Bootstrap，jQuery 和 popper.js（Bootstrap 组件需要）：



```
yarn add bootstrap jquery popper.js
```



接下来，我们将在 `app/javascript` 目录下，创建名为 `stylesheets` 的文件夹（完成路径`**app/javascript/stylesheets**`），行为类似 `app/assets/stylesheets`，不同之处则为此处的样式文件通常与 `javascript` 打包在一起。（例如：bootstrap 模块将其 CSS 打包在一起）。



在 `app/javascript/stylesheets` 目录下，创建名为 `application.scss` 的文件（类似资源文件夹）。然后引入 bootstrap CSS 在此文件：



```
/* app/javascript/stylesheets/application.scss */
@import "~bootstrap/scss/bootstrap";
/* this will import the scss file inside node_modules/bootstrap/scss/bootstrap.scss
```



可选步骤 ：也许将样式文件夹放在 javascript 目录下让你感到补输入。如果你希望组织资源文件夹中的所有样式文件，可以在 `app/assets/stylesheets/application.scss` （由 .css 改名为 .scss）中引入座位替代。



```
/* app/assets/stylesheets/application.scss */

@import '../../../node_modules/bootstrap/scss/bootstrap';
```



在 **app/javascript/packs/application.js**，引入 bootstrap 和打包的 CSS，`webpack` 将引入它的依赖：



```
// app/javascript/packs/application.js

require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")

// import the bootstrap javascript module
import "bootstrap"

// import the application.scss we created for the bootstrap CSS (if you are not using assets stylesheet)
import "../stylesheets/application"
```



![import css](https://rubyyagi.s3.amazonaws.com/3a-bootstrap-jquery-rails-6/import_css.png)



接下来，我们在布局文件（`app/views/layouts/application.html.erb`） 中，添加 `stylesheet_pack_tag` 语句来引用资源文件。



```
<!-- app/views/layouts/application.html.erb-->

<!DOCTYPE html>
<html>
  <head>
    <title>Bootstraper</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    
    <!-- This refers to app/javascript/stylesheets/application.scss-->
    <%= stylesheet_pack_tag 'application', 'data-turbolinks-track': 'reload' %>

    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
....
```



因为 Bootstrap 的运行，需要依赖 jQuery 和 pop.js，所以我们需要使用 `ProvidePlugin` 来引入 `jQuery` 和 `popper.js` 模块，以使他们可以使用（如 Bootstrap）。



在现代模块化 javascript 开发中，除非你手动引入变量，否则模块通常不直接访问外部变量，这鼓励了开发者编写独立的模块，这些模块具有内聚性，且没有隐形的依赖（外部变量）。





