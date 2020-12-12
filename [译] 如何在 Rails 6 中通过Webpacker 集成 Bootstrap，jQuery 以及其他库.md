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



