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



有了这些功能，其实搭建一个简单的展示型网站已经够了。但一个博客系统，需要可以自动索引我们发布的日志。如果没有索引，我们每发布一篇日志，就必须往 index 页面中添加一次引用，这样太麻烦了。



Middleman 官方提供了 Middleman-blog 扩展，解决了这个问题：



[middleman/middleman-blog: Middleman : Blog Engine Extension (github.com)](https://github.com/middleman/middleman-blog)



毕竟官方扩展，官网文档也专门有一个章节来介绍。



[Middleman: Blogging (middlemanapp.com)](https://middlemanapp.com/basics/blogging/)



这个扩展的使用很简单，分两步：



1、Gemfile 文件添加扩展引用并安装：

```
gem "middleman-blog", "~> 4.0"
```



2、`config.rb` 文件增加针对扩展的配置：

```
activate :blog do |blog|
  # set options on blog
end
```



这是针对已有项目的，如果是一个的日志系统，可以直接通过模板，拉回来已经配置好的版本：

```
middleman init MY_BLOG_PROJECT --template=blog
```



这个扩展会自动索引 source 目录下的所有文章源文件，默认使用的是 `markdown` 格式 。

我们可以使用 `middleman article article_title` 来生成新的文章，生成后的文章会在 `source` 目录中，文件的标题默认为：`{year}-{month}-{day}-{title}.html.markdown`。随便打开一个刚刚生成的文件，会发现顶部有一些被包裹的 yaml 格式的数据：



``` 
---
title: My Middleman Blog Post
---
```



我们可以在文章的顶部定义一些该文章的元信息，如标题，tag，发布日期等。















