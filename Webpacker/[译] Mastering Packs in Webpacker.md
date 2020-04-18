
翻译自：[https://prathamesh.tech/2019/09/24/mastering-packs-in-webpacker/](https://prathamesh.tech/2019/09/24/mastering-packs-in-webpacker/)

一个新的 Rails 6 App 在 `app/javascript`目录中创建一系列文件，我们在这些文件里编写 JavaScript 代码。

```
Projects/scratch/better_hn  master ✗ 2.6.3                                                        
▶ tree app/javascript
app/javascript
├── channels
│   ├── consumer.js
│   └── index.js
└── packs
    └── application.js

2 directories, 3 files
```

包目录中包含的 webpack 的入口点，webpack 根据入口点决定如何编译。在 Rails 6 app 中，定义如下：

```
require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")
```

> 入口点类似 asset pipe 生成的 `app/assets/application.js` 文件。

`require` 语句在这里和 asset pipeline 中的 `require` 不一样。这里的 `require` 语句可以引用本地代码，也可以引用 NPM 包。举个例子，上面的代码头三行就引用了三个 NPM 包：Rails UJS，Turbolinks 和 Active Storage；最后一样引用的 `channels` 则在 `app/javascript/channels/index.js` 文件中。

> 在引入目录时，Webpack 约定 index.js 作为入口文件。

在引入文件或目录时，Webpacker 主要使用 `import` 和 `require` 语句，没有专门的引入语句。这是和 Webpack 的主要区别。

```
// app/javascript/packs/application.js

import React from 'react'
import ReactDOM from 'react-dom'
```

可以将项目所用到的 JavaScript 代码写到包文件中，但更好的做法是保持包文件的整洁，只在其中引用模块的代码，然后将项目中所用到的代码放在包的外面。

> 保持包文件最小，以及在其中引用项目中所用到模块的代码。

所以，对于一个 React 项目，我们将引入组件到 DOM 的代码放在包文件中，将对组件的操作放在包目录的外面。

```
// app/javascript/packs/application.js
import ReactDOM from 'react-dom'
import HelloWorld from '../components/hello'

document.addEventListener('DOMContentLoaded', () => {
  ReactDOM.render(
    <HelloWorld name="Webpack" />,
    document.body.appendChild(document.createElement('div')),
  )
})
```

```
// app/javascript/components/hello.js

import React from 'react'
import PropTypes from 'prop-types'

const HelloWorld = props => (
  <div>Hello {props.name}!</div>
)

Hello.defaultProps = {
  name: 'David'
}

Hello.propTypes = {
  name: PropTypes.string
}

export default HelloWorld
```

Webpacker 在组织 JavaScript 代码方面非常自由。只要记住 `packs` 目录是特殊的，它作为 Webpacker 的入口。我比较喜欢增加一个 `components` 目录，将 React 组件放进去。除了默认的 `application` 包作为默认入口以外，我也增加了一个 `admin` 包，用来放管理相关的代码。

使用 `Sprockets` 的时候，如果想增加自定义入口文件，我们必须要修改 Rails 的预编译列表。

```
Rails.application.config.assets.precompile += %w[admin.js]
```

当使用 Webpacker 时，我们不需要修改任何预编译设它就可以工作的很好。因为 `packs` 目录的内容都被用做 Webpacker 的入口点。

一般来说，我们应该使用 `app/javascript` 目录来组织我们的 JavaScript 代码。在我的应用中，`app/javascript` 的目录结构如下：

```
app/javascript
├── admin
├── channels
├── login
└── packs
    ├── admin.js
    ├── application.js
    └── login.js
```

当我们编译 JavaScript 时，输出如下：

```
▶ ./bin/webpack-dev-server
ℹ ｢wds｣: Project is running at http://localhost:3035/
ℹ ｢wds｣: webpack output is served from /packs/
ℹ ｢wds｣: Content not from webpack is served from /Users/prathamesh/Projects/scratch/better_hn/public/packs
ℹ ｢wds｣: 404s will fallback to /index.html
ℹ ｢wdm｣: Hash: 5387bbdba96d7150c792
Version: webpack 4.39.2
Time: 2753ms
Built at: 09/24/2019 12:23:20 AM
                                     Asset       Size       Chunks             Chunk Names
          js/admin-67dd60bc5c69e9e06cc3.js    385 KiB        admin  [emitted]  admin
      js/admin-67dd60bc5c69e9e06cc3.js.map    434 KiB        admin  [emitted]  admin
    js/application-d351b587b51ad82444e4.js    505 KiB  application  [emitted]  application
js/application-d351b587b51ad82444e4.js.map    569 KiB  application  [emitted]  application
          js/login-1c7b2341998332589ec0.js    385 KiB        login  [emitted]  login
      js/login-1c7b2341998332589ec0.js.map    434 KiB        login  [emitted]  login
                             manifest.json  958 bytes               [emitted]
```

除了生成一系列带有指纹的文件和源映射文件，还生成了包含所有这次编译处理信息及文件列表的 `manifest.json` 文件。Rails 根据这个文件的信息来实现 `javascript_pack_tag`，将其转换为引入相关处理后文件的语句。比如 `javascript_pack_tag('admin')` 将会转换成 `js/admin-67dd60bc5c69e9e06cc3.js`。一个 `manifest.json` 文件的内容 [示例](https://gist.github.com/prathamesh-sonpatki/25c8657fc811680af37cf79fcb3c3792)。

现在，我们的包已经准备好了，接下来把他们放在布局文件中进行使用。在我的示例中，`login` 包只被用于 `login` 布局文件，`login` 包被从 `application` 包拆分出来，专门用于用户登录的时候。`admin` 布局则引用了一个从 `application` 包中拆出来的 `admin` 包。在布局文件可以引用任何包。

```
<body>
  <%= javascript_pack_tag "application" %>
  <%= javascript_pack_tag "admin" %>
</body>
```

总结一下本文内容：

- 保持包文件最小，从其他文件中引入必要代码
- `app/javascript/packs` 目录只放包文件
- 你可以根据需要，自由的在 `app/javascript` 目录中组织 JavaScript 代码
- 注意 Webpack 的输出，以及打包的大小
- 根据需要组织包文件，只在需要的地方引入必要的包

如果你想了解更多有关 Webpacker 和 Rails 6 的知识，[be with me on the Road to Rails 6](https://prathamesh.tech/road-to-rails-6/)。











