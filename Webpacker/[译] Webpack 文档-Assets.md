静态资源，比如图片、字体的支持是开箱可用的。你可以链接他们在你的 JavaScript 代码中，他们会自动被编译。

### 从 node 模块引入

你可以引入样式从 `node_modules` 使用下面的语法。请注意你的样式文件一直带有`[pack_name].css`：

```
// app/javascript/styles.sass
// ~ to tell webpack that this is not a relative import:

@import '~@material/animation/mdc-animation'
@import '~bootstrap/dist/css/bootstrap'
```

```
// Your main app pack
// app/javascript/packs/app.js

import '../styles'
```

```
<%# In your views %>

<%= javascript_pack_tag 'app' %>
<%= stylesheet_pack_tag 'app' %>
```

### 从 Sprockets 引入

同样可以从 `Sprockets` 引入预编译的资源。给你的 JavaScript 文件添加 `.erb` 扩展名，然后就可以通过 helper 方法使用 `Sprockets` 资源了：

```
<%# app/javascript/my_pack/example.js.erb %>

<% helpers = ActionController::Base.helpers %>
const railsImagePath = "<%= helpers.image_path('rails.png') %>"
```

这项特性通过 `rails-erb-loader` loader 规则激活，配置文件在`config/webpack/loaders/erb.js`。

### 使用 babel 模块

可以通过使用 [babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver) 直接引用 `app/assets/**` 中的资源。

```
yarn add babel-plugin-module-resolver
```

插件需要修改 `babel.config.js` 中的配置，下面是例子：

```
{
  plugins: [
    [require("babel-plugin-module-resolver").default, {
      "root": ["./app"],
      "alias": {
        "assets": "./assets"
      }
    }]
  ]
}
```

然后这样使用：

```
// Note: we don't have to do any ../../ jazz

import FooImage from 'assets/images/foo-image.png'
import 'assets/stylesheets/bar'
```

### 在你的 Rails 视图中链接

通过使用 `asset_pack_path` 和 `image_pack_tag` 方法来得到资源路径，你可以在你的  js app 中，链接 `js/images/styles/fonts` 多种资源。在你想使用 `<link rel="prefetch">`、`<img />` 这种标签的时候，这些方法用的到。

```
app/javascript:
  - packs
    - app.js
  - images
    - calendar.png
```

```
// app/javascript/packs/app.js (or any of your packs)

// import all image files in a folder:
require.context('../images', true)
```

```
<%# Rails view, for example app/views/layouts/application.html.erb %>

<img src="<%= asset_pack_path 'media/images/calendar.png' %>" />
<% # => <img src="/packs/media/images/calendar-k344a6d59eef8632c9d1.png" /> %>

<%= image_pack_tag 'media/images/calendar.png' %>
<% # => <img src="/packs/media/images/calendar-k344a6d59eef8632c9d1.png" /> %>

<%# no path resolves to default 'images' folder: %>
<%= image_pack_tag 'calendar.png' %>
<% # => <img src="/packs/media/images/calendar-k344a6d59eef8632c9d1.png" /> %>
```

注意，当你用到`app/javascript`子目录结构的时候，你需要添加`media/`前缀（不是`/media/`）。[更多例子](https://github.com/rails/webpacker/blob/0b86cadb5ed921e2c1538382e72a236ec30a5d97/test/helper_test.rb#L37)

