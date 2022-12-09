有个 Rails 项目，初始化的时候，选择了 importmap，想学习一下。后面发现 importmap 和当前主流前端方案差异化过大，简化开发流程的优势也不明显，所以打算迁移为 jsbundling-rails 和 cssbundling-rails 的方案。基本上就是照着文档搞，本文为记录。



依赖 gem：

[rails/cssbundling-rails: Bundle and process CSS in Rails with Tailwind, PostCSS, and Sass via Node.js. (github.com)](https://github.com/rails/cssbundling-rails)

[rails/jsbundling-rails: Bundle and transpile JavaScript in Rails with esbuild, rollup.js, or Webpack. (github.com)](https://github.com/rails/jsbundling-rails)



## 删除 importmap 依赖



1. Gemfile 文件中，删除 `importmap-rails`
2. 删除 `config/importmap.rb`，这里需要保存一下当前引入的前端库
3. 删除 `bin/importmap`
4. `assets/manifest.js` 文件中，删掉对本地文件引用，修改后：

```
//= link_tree ../images
//= link_tree ../builds
```



## 安装 jsbundling-rails



1. 执行`./bin/bundle add jsbundling-rails`
2. `./bin/rails javascript:install:esbuild`

我喜欢使用 esbuild，编译速度快，配置相对简单。这里也可以选择 webpack 和 rollup。



执行完毕后，此时会创建 `application.js` 文件，使用 `yarn add package_name` 安装包，然后在 `application` 文件中，进行引用即可。此时 `application.html.erb` 文件头部，已经插入了响应的文件。



文档中也提到，如果你想把 css 样式也在此引入，创建 `application.css` 文件，然后引入即可，和其他前端项目没有区别。



## 安装 cssbundling-rails



如果你想将 css 样式，单独拆分出来，可以使用 `cssbundling-rails`。



1. `./bin/bundle add cssbundling-rails`
2. `./bin/rails css:install:[tailwind|bootstrap|bulma|postcss|sass]`



有多种前端方案选择，执行完毕后，会引入对应的样式库，以及生成文件。在其中编写样式即可。



