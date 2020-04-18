原文：[https://github.com/rails/webpacker/blob/master/docs/env.md](https://github.com/rails/webpacker/blob/master/docs/env.md)

### 环境变量

Webpacker 内置对环境变量的支持，开箱即用。举个例子，如果你带着环境变量运行开发服务器：

```
FOO=hello BAR=world ./bin/webpack-dev-server
```

你可以在 JavaScript 代码中通过`process.env`来访问环境变量：

```
FOO=hello BAR=world ./bin/webpack-dev-server
```

类似[dotenv](https://github.com/bkeepers/dotenv)，你可以通过 `.env` 文件对环境变量配置。

开发模式下，如果你使用[Foreman](http://ddollar.github.io/foreman)或者[Invoker](http://invoker.codemancers.com/)来启动 webpack 服务端，你可以直接使用`.env`文件，无需额外配置，这两个工具都内置了对.env的基本支持。（Invoker 额外对 `.env.local` 提供支持）。

如果你不打算使用 Foreman/Invoker，或者你想对 `.env` 文件更深入的控制，你可以使用[dotenv npm package](https://github.com/motdotla/dotenv)。这个包为你提供了"Ruby 类似" 的 dotenv 支持：

```
yarn add dotenv
```

```
// config/webpack/environment.js

...
const { environment } = require('@rails/webpacker')
const webpack = require('webpack')
const dotenv = require('dotenv')

const dotenvFiles = [
  `.env.${process.env.NODE_ENV}.local`,
  '.env.local',
  `.env.${process.env.NODE_ENV}`,
  '.env'
]
dotenvFiles.forEach((dotenvFile) => {
  dotenv.config({ path: dotenvFile, silent: true })
})

environment.plugins.prepend('Environment', new webpack.EnvironmentPlugin(JSON.parse(JSON.stringify(process.env))))

module.exports = environment
```

警告：一起使用 Foreman/Invoker 与 npm dotenv 会引起混乱，Foreman/Invoker 的变量加载会覆盖 dotenv 的变量加载。