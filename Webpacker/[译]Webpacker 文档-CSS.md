[https://github.com/rails/webpacker/blob/master/docs/css.md](https://github.com/rails/webpacker/blob/master/docs/css.md)


## CSS，Sass 和 SCSS

Webpacker 支持直接在 JavaScript 中导入 CSS，Sass 和 SCSS。

导入并加载样式文件分为两步处理：

1. 告诉 webpack 哪些文件需要被编译，并让它知道如何加载

当你执行 `import '../scss/application.scss'` 的时候，你是在告诉 webpack 引入 `application.scss` 并编译。这不意味着它将被编译近你的 JavaScript 文件中，webpack 只是编译它并知道如何载入这个文件。（文件的编译方式依赖你配置的 loader（css-loader，sass-loader，file-loader，等等）。

2. 你需要在你的视图中加载编译后的样式

在生产环境引入样式，你需要使用 `stylesheet_pack_tag` 标签带上引入样式的 javascript 文件名。

当执行 `<%= stylesheet_pack_tag 'application' %>`，Rails 运行时会加载证确的 webpack 编译后的"资源路径"。


### 为你的JS App 中引入全局样式

```
// app/javascript/hello_react/styles/hello-react.sass

.hello-react
  padding: 20px
  font-size: 12px
```
```
// React component example
// app/javascript/packs/hello_react.jsx

import React from 'react'
import helloIcon from '../hello_react/images/icon.png'
import '../hello_react/styles/hello-react'

const Hello = props => (
  <div className="hello-react">
    <img src={helloIcon} alt="hello-icon" />
    <p>Hello {props.name}!</p>
  </div>
)
```

###为你的 JS App 引入局部样式

以 `.module.*` 结尾的样式文件被作为[CSS Modules](https://github.com/css-modules/css-modules)。
```
// app/javascript/hello_react/styles/hello-react.module.sass

.helloReact
  padding: 20px
  font-size: 12px
```
```
// React component example
// app/javascript/packs/hello_react.jsx

import React from 'react'
import helloIcon from '../hello_react/images/icon.png'
import styles from '../hello_react/styles/hello-react'

const Hello = props => (
  <div className={styles.helloReact}>
    <img src={helloIcon} alt="hello-icon" />
    <p>Hello {props.name}!</p>
  </div>
)
```
提示：声明的 class 会被作为 JavaScript 对象的属性被引用。

### 为你的 TypeScript App 引入局部样式

在 TypeScript 应用中使用 CSS 模块与 JavaScript 应用有一些不同。CSS / Sass 文件定义：

```
// app/javascript/hello_react/styles/hello-react.module.sass

.helloReact
  padding: 20px
  font-size: 12px
```

 必须给样式定义类型：

```
export const helloReact: string;
```

然后这样引入样式：

```
// React component example
// app/javascripts/packs/hello_react.tsx

import React from 'react'
import helloIcon from '../hello_react/images/icon.png'
import * as styles from '../hello_react/styles/hello-react.module.sass'

const Hello = props => (
  <div className={styles.helloReact}>
    <img src={helloIcon} alt="hello-icon" />
    <p>Hello {props.name}!</p>
  </div>
)
```
可以使用 `typed-scss-modules` 包来自动生成样式的定义，将其添加到开发环境：

```
yarn add typed-scss-modules --dev
```

修改 `package.json` 文件

```
"scripts": {
  "gen-typings": "yarn run tsm app/javascript/**/*.sass",
  "watch-typings": "yarn run tsm app/javascript/**/*.sass -w"
},
```

现在，写完 CSS 之后，可以使用 `yarn gen-typings` 命令来生成样式的定义，或者使用 `yarn watch-typings` 监视目录，自动生成样式。


### 在 Rails 视图中链接你的样式

Webpack 使用[mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin)来处理你所用到的样式文件，将它们编译成文件名为`[pack_name].css`的包，然后你可以在视图中使用`stylesheet_pack_tag` 函数进行插入。

```
<%= stylesheet_pack_tag 'hello_react' %>
```

如果 `webpacker.yml` 配置文件中的 `extract_css` 选项为 true，Webpacker 输出 css 文件；否则 `stylesheet_pack_tag` 标签返回 nil。

### 添加 Bootstrap

你可以通过 Yarn 来添加 Bootstrap，或者其他可用的 npm 包：

```
yarn add bootstrap
```

引入 Bootstrap 和 主题 CSS 在你的 `app/javascript/packs/app.js` 文件：

```
import 'bootstrap/dist/css/bootstrap'
import 'bootstrap/dist/css/bootstrap-theme'
```

或者在你的 ` app/javascript/app.sass` 文件：

```
// ~ to tell that this is not a relative import

@import '~bootstrap/dist/css/bootstrap'
@import '~bootstrap/dist/css/bootstrap-theme'
```

### 对 CSS 的再处理

Webpacker 通过[postcss-loader](https://github.com/postcss/postcss-loader)提供了开箱即用的 CSS 再处理功能，配置文件在根目录下的 `postcss.config.js` 文件中，并引入了默认的插件。

```
module.exports = {
  plugins: [
    require('postcss-import'),
    require('postcss-flexbugs-fixes'),
    require('postcss-preset-env')({
      autoprefixer: {
        flexbox: 'no-2009'
      },
      stage: 3
    })
  ]
}
```

### 配合 [vue-loader](https://github.com/vuejs/vue-loader) 使用 CSS

Vue 模板需要在应用程序中加载样式，以便 CSS 可以证确工作。这是加载入口点的额外要做的。在加载样式表中，还将加载组件的样式。

```
<%= stylesheet_pack_tag 'hello_vue' %>
<%= javascript_pack_tag 'hello_vue' %>
```

### 解决 url loader

自从 `Sass/libsass` 不提供 url 重写后，所有输出的链接资源必须是相对地址。添加缺失的 url 重写使用 resolve-url-loader。直接放在 `sass-loader` 加载后面。

```
yarn add resolve-url-loader
```

```
// webpack/environment.js
const { environment } = require('@rails/webpacker')

// resolve-url-loader must be used before sass-loader
environment.loaders.get('sass').use.splice(-1, 0, {
  loader: 'resolve-url-loader'
});

module.exports = environment
```

### 配合 TypeScript 一起工作

为了让 CSS 和 TypeScript 一起工作，你有两个选择。你可以使用 `require` 绕过 TypeScript 中的 `import`。

```
const styles = require('../hello_react/styles/hello-react');
```

你也可以使用 [typings-for-css-modules-loader](https://github.com/Jimdo/typings-for-css-modules-loader) 替代 `css-loader` 自动生成 typescript `.d.ts` 文件以使用想要用的 `css/scss` 样式：

```
// app/javascript/packs/hello_react.jsx
import * as styles from '../hello_react.styles/hello-react.module.scss';
```

```
yarn add --dev typings-for-css-modules-loader
```

```
// webpack/environment.js
const { environment } = require('@rails/webpacker')

// replace css-loader with typings-for-css-modules-loader
environment.loaders.get('moduleSass').use = environment.loaders.get('moduleSass').use.map((u) => {
  if(u.loader == 'css-loader') {
    return { ...u, loader: 'typings-for-css-modules-loader' };
  } else {
    return u;
  }
});
```


