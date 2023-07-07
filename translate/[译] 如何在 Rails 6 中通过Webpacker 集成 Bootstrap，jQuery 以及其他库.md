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



![isolated modules](https://rubyyagi.s3.amazonaws.com/3a-bootstrap-jquery-rails-6/provide1.png)

通过使用 ProvidePlugin 插件并执行 [shimming](https://webpack.js.org/guides/shimming/) 操作，我们可以在其他模块中访问变量。



>  [`ProvidePlugin`](https://webpack.js.org/plugins/provide-plugin) 插件通过 webpack，使变量得以在编译后的不同模块中共享。如果 webpack 发现某个变量被用到，他将引入最终打包文件中的对应的模块。



![provide plugin](https://rubyyagi.s3.amazonaws.com/3a-bootstrap-jquery-rails-6/provide2.png)

打开 `config/webpack/environment.js` 文件，然后添加 ProvidePlugin 插件，并使用 "$"，"jQuery" 以及 "Propper" 插件。



```
// config/webpack/environment.js
const { environment } = require('@rails/webpacker')
const webpack = require("webpack");

// Add an additional plugin of your choosing : ProvidePlugin
environment.plugins.append(
  "Provide",
  new webpack.ProvidePlugin({
    $: "jquery",
    jQuery: "jquery",
    Popper: ["popper.js", "default"] // for Bootstrap 4
  })
);

module.exports = environment
```



在改变 `environment.js` 文件后，你也许需要重启你的 rails 服务端以更新。



现在，你可以在你的视图代码中，使用 Bootstrap 啦。



### 在视图代码中使用 jQuery



此时，如果你尝试在你的布局代码中使用 jQuery（如 html.erb，html.haml 等），你将会收到报错："Uncaught ReferenceError, $ is not defined"：

![uncaught reference error](https://rubyyagi.s3.amazonaws.com/3a-bootstrap-jquery-rails-6/uncaught_reference.png)



这是因为我们虽然设置了 "$" 在全局作用域内可访问，但 "$" 并不在全局作用域。ProvidePlugin 只会使其在其他模块可见，但并不一定在全局。



![not on global](https://rubyyagi.s3.amazonaws.com/3a-bootstrap-jquery-rails-6/no_access.png)

要解决这个问题，我们可以将 "$" 和 "jQuery" 变量挂载到全局作用域（或者 window 作用域，它也背用于浏览器的顶级作用域）。在打包文件中（**app/javascript/packs/application.js**）挂载到全局作用域：



```
// app/javascript/packs/application.js

require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")

import "bootstrap"
import "../stylesheets/application"

var jQuery = require('jquery')

// include jQuery in global and window scope (so you can access it globally)
// in your web browser, when you type $('.div'), it is actually refering to global.$('.div')
global.$ = global.jQuery = jQuery;
window.$ = window.jQuery = jQuery;
```



### 使用依赖 jQuery 的库



举个例子，我想使用  [DateRangePicker](https://www.daterangepicker.com/)，我常用它作为选择日期的组件。这个库依赖 jQuery 和 moment.js 实现一些功能。



首先使用 yarn 安装 moment.js 和 daterangepicker，当然，他们在 yarn 的仓库：



```
yarn add moment daterangepicker
```



接下来，我们将在全局作用域引入 moment，这是为了让 DateRangePicker 可以引用到他，打开打包文件 `**app/javascript/packs/application.js**,`，添加 moment 的引用，并绑定到全局作用域。



不要忘记引入 daterangepicker，并确保它在 bootstrap 和 jQuery 之前被引入。



```
// app/javascript/packs/application.js

require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")

import "bootstrap"
import "../stylesheets/application"

var moment = require('moment')

var jQuery = require('jquery')

import "daterangepicker"

// include jQuery in global and window scope (so you can access it globally)
// in your web browser, when you type $('.div'), it is actually refering to global.$('.div')
global.$ = global.jQuery = jQuery;
window.$ = window.jQuery = jQuery;

// include moment in global and window scope (so you can access it globally)
global.moment = moment;
window.moment = moment;
```



由于 DateRangePicker 有自己的 CSS 文件，我们需要在样式打包文件中，引入它。（**app/javascript/stylesheets/application.scss**）



```
/* app/javascript/stylesheets/application.scss */

@import "~bootstrap/scss/bootstrap";

@import "../../../node_modules/daterangepicker/daterangepicker.css";
```



因为一些原因，`"~"` 在 .css 文件中，并不能工作，所以我们不能使用 @import ~daterangepicker/daterangepicker.css。所以，我们必须使用相对路径，向上三个文件夹，使用 `../../../node_modules` 来在 `/app/javascript/styles` 中引用  `/node_modules` 目录。



如果 node 模块包含 .scss 文件，你可以在前面使用 `~` 符号来进行导入，如导入相对地址的 node_modules 文件一样。



现在，在你的视图代码，你可以像下面这样访问 daterangepicker 的输入：



```
<h1>Page#index</h1>
<p>Find me in app/views/page/index.html.erb</p>
 
<input type="text" name="dates"/>
<script>
$( document ).ready(function() {
  $('input[name="dates"]').daterangepicker();
});
</script>
```



### 使用 npm 中不存在的 javascript 库



如何使用 npm 仓库中不存在的 javascript 库？有一些库只有单独的 .js 文件，如何使用 webpacker 安装并使用他们？



比如，我打算使用 [select2](https://select2.org/getting-started/installation)，它有一个封装好的 gem（[select2-rails](https://github.com/argerim/select2-rails)），但我们想下载它的 .js 和 .css 引入到我们的 Rails 应用中。我们可以下载已经发行的代码：https://github.com/select2/select2/tags，选择一个版本，解压缩，并打开 "dist" 文件夹，然后选择 `select2.min.css` 和 `select2.min.js`。



通常我会为自定义的 javascript 类库创建文件夹，并放到 `app/javascript` 目录中，在本案例中，`app/javascript/select2` 是我要放的地方，将文件放好后，如图所示：



![js css location](https://rubyyagi.s3.amazonaws.com/3a-bootstrap-jquery-rails-6/custom_js.png)



接着在你应用打包文件中（app/javascript/packs/application.js），引入 select2.min.js 文件：



```
// app/javascript/packs/application.js

import "../select2/select2.min"
```



我们可以省略 `.js` 文件扩展名，确保路径正确。



接下来，我们需要在 `stylesheet` 打包文件中引入我们先前创建的 css 文件（**app/javascript/stylesheets/application.scss**）



```
/* app/javascript/stylesheets/application.scss */
@import "../select2/select2.min.css"
```



现在，我们可以在视图代码中使用 select2 中的方法：



```
<select class="js-example-basic-single" name="state">
  <option value="AL">Alabama</option>
  <option value="WY">Wyoming</option>
</select>
 

<script>
$( document ).ready(function() {
  $('.js-example-basic-single').select2();
});
</script>
```



