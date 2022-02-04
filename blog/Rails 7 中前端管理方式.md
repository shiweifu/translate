Rails 7 中前端管理方式



Rails 7 抛弃了 Rails 6 中引入的 Webpacker，以及一把梭的前端资源管理方式，分为三种，来应对不同场景：



1. 传统方式 

使用传统方式管理 CSS 资源，通过新引入的 importmap 管理 JS。



2. jsbundling 以及 cssbundling

新引入的两个 gem，通过这两个预编译器，来管理现代前端 JS 和 CSS。

两者从 npm 包中，打包内容，然后将打包好的文件输出为 `app/assets/builds/application.js ` 和 `app/assets/builds/application.css`，然后再交给 sprockets ，进行哈希和压缩处理。

这种方式是用于替换 Rails 6 中，Webpacker 的。

jsbundling 可选打包方案：

- esbuild
- rollup.js
- webpack



cssbundling 可选前端框架：

- bulma
- bootstrap
- tailwind
- postcss
- dartsass



Webpacker 的 github 页面上写着 `Webpacker has been retired 🌅`，Rails 官方不会再维护这个项目了。如果你仍旧想用 Rails 6 的方式来管理前端，你可以使用 [shakacode/shakapacker: Use Webpack to manage app-like JavaScript modules in Rails (github.com)](https://github.com/shakacode/shakapacker)，这个项目宣称是 Webpacker 的继任者，会一直维护下去。



3. 纯 API 模式

