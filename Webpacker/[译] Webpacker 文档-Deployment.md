翻译自：[Webpacker: Deployment](https://github.com/rails/webpacker/blob/master/docs/deployment.md)


### 部署

Webpacker 将新的 `webpacker:compile` 任务连接到了 `assets:precompile`，当你运行 `assets:precompile` 时它会被执行。当没有使用 Sprockets 的情况下，`webpacker:compile`，将是 `assets:precompile` 的别名。

`javascript_pack_tag` 和 `stylesheet_pack_tag` 辅助方法将在编译后的文件，自动插入正确的 HTML 标签。就像 `asset pipeline` 做的一样。

默认输出不同环境变量下略有不同：

```
  <!-- In development mode with webpack-dev-server -->
  <script src="http://localhost:8080/calendar-0bd141f6d9360cf4a7f5.js"></script>
  <link rel="stylesheet" media="screen" href="http://localhost:8080/calendar-dc02976b5f94b507e3b6.css">
  <!-- In production or development mode -->
  <script src="/packs/js/calendar-0bd141f6d9360cf4a7f5.js"></script>
  <link rel="stylesheet" media="screen" href="/packs/css/calendar-dc02976b5f94b507e3b6.css">
```

### Heroku

当在 Heroku 中部署你的 Webpacker app 的时候，你需要些做一些配置：

```
heroku create my-webpacker-heroku-app
heroku addons:create heroku-postgresql:hobby-dev
heroku buildpacks:add heroku/nodejs
heroku buildpacks:add heroku/ruby
git push heroku master
```

我们刚刚做了以下工作：

- 在 Heroku 上创建了一个 App
- 为这个 App 创建了一个 Postgres 数据库（你的 App 使用 Heroku 提供的 Postgres 
 数据库服务）
- 为你的 App 增加 NodeJS 和 Ruby 构建包。这确保你的程序编译的时候，`npm` 或者 `yarn` 都可以正确运行。当然 Ruby 也可以正确运行
 - 将代码推送到 Heroku，以及开始部署


