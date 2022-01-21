[Middleman](https://middlemanapp.com/) 是一个 Ruby 语言的静态网站生成器，官网这样介绍：



> Middleman is a static site generator using all the shortcuts and tools in modern web development.



Middleman 通过使用现代、便捷的工具，方便的构建静态网站。



博客系统是一个基本的 CMS 系统，相较于 `wordpress`、`typecho` 这类动态日志系统，静态日志系统的好处是操作流程简单透明，占用资源少，部署方面。本文的目标是构建一个包含日志和图片两个模块的静态日志系统，最终这个系统会用于部署我的个人博客系统，代码会全部开源到 github 上，会涉及以下技术或框架：



- Middleman 的基本使用
- Middleman-blog 扩展插件
- Bulma 布局和组件
- 资源打包
- 静态网站的部署



Middleman 的目录结构如下：



```
mymiddlemansite/
+-- .gitignore
+-- Gemfile
+-- Gemfile.lock
+-- config.rb
+-- build
+-- data
+-- source
    +-- images
    ¦   +-- background.png
    ¦   +-- middleman.png
    +-- index.html.erb
    +-- javascripts
    ¦   +-- all.js
    +-- layouts
    ¦   +-- layout.erb
    +-- stylesheets
        +-- all.css
        +-- normalize.css
```



可以看到，涉及网站核心的内容，都在 source 目录下：

- layouts 目录下是布局文件
- stylesheets 目录下存样式文件
- javascripts 目录下是 JS 脚本文件
- images 下存放网站所用到的图片文件

外部的目录：

- data 目录下存放网站所用到的数据文件，如 yaml，json 等。Middleman 会解析这些数据文件中的内容，然后在 erb 文件中，可以直接使用这些数据

- build 是构建后的静态网站，部署只需要这个目录中的文件就够了



有了这些功能，其实搭建一个简单的展示型网站已经够了。但一个博客系统，我们写完日志，并不想每一篇日志都往 index 页面中添加引用，这样太麻烦了。



