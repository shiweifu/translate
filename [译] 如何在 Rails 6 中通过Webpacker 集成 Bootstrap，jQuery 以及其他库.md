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

