[Middleman](https://middlemanapp.com/) 是一个 Ruby 语言实现的静态网站生成器，官网这样介绍：



> Middleman is a static site generator using all the shortcuts and tools in modern web development.
>
> Middleman 使用现代、便捷的工具，方便的构建静态网站。



本文通过实现一个静态的博客系统，来介绍 Middleman 的基本使用。

相较于 `wordpress`、`typecho` 这类动态博客系统，静态博客系统的好处是操作流程简单透明，对部署服务器要求低，生产环境下占用资源也少。本文的目标是构建一个包含日志和图片两个模块的静态日志系统，最终这个系统会用于部署我的个人博客系统，代码会全部开源到 github 上，会涉及以下技术或框架：



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

其他相关目录：

- data 目录下存放网站所用到的数据文件，如 yaml，json 等。Middleman 会解析这些数据文件中的内容，然后在 erb 文件中，可以直接使用这些数据

- build 是构建后的静态网站，部署只需要这个目录中的文件就够了



有了这些功能，其实搭建一个简单的展示型网站已经够了。但一个博客系统，需要可以自动索引我们发布的日志。如果没有索引，我们每发布一篇日志，就必须往 index 页面中添加一次引用，这样太麻烦了。



Middleman 官方提供了 Middleman-blog 扩展，解决了这个问题：



[middleman/middleman-blog: Middleman : Blog Engine Extension (github.com)](https://github.com/middleman/middleman-blog)



官网文档也专门有一个章节来介绍 Middleman-blog 的使用。



[Middleman: Blogging (middlemanapp.com)](https://middlemanapp.com/basics/blogging/)



这个扩展的使用很简单，如果是一个已有的项目，引入扩展分两步：



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



如果是一个新的日志系统，可以直接通过官方提供的模板，拉回来已经集成配置好的版本：

```
middleman init MY_BLOG_PROJECT --template=blog
```



有了这个扩展，source 目录下的所有 markdown（默认使用markdown）格式的文章源文件，都会被索引，并且注入到 erb 文件中，可以直接调用并渲染。

我们可以使用 `middleman article article_title` 命令来生成新的文章，新生成的文件的标题默认为：`{year}-{month}-{day}-{title}.html.markdown`，保存在 source 文件夹中。随便打开一个刚刚生成的文件，会发现顶部有一些被包裹的 yaml 格式的数据：



``` 
---
title: My Middleman Blog Post
---
```



Middleman-blog 保留了一些关键字，作为文章的元信息：

- tag：文章的标签
- title：文章标题
- date：文章日期



这些保留的元信息 `Middleman-blog` 索引和处理。

我们也可以定义任意与文章相关的其他信息，这些信息都可以在包含文章的 erb 文件中访问到：

```
<%= current_article.data.from_website %>
```





---



项目中有 `layout.erb` 文件，这是默认的布局文件，所有渲染后的内容，都被加入到 `layout.erb` 中。



可以在 `config.rb` 中来配置这个文件的名字。



---



有关 Ruby 环境的安装和配置，请参考：

[rbenv 使用指南 · Ruby China (ruby-china.org)](https://ruby-china.org/wiki/rbenv-guide)

本文默认您已经安装好了 Ruby 环境。



安装 `middleman`：

```
gem install middleman
```



新建项目：

```
middleman init MY_BLOG_PROJECT --template=blog
```



运行项目：

```
middleman server
```



此时，如果一切正常，我们的项目已经成功跑起来了，默认端口是 `:4321`。



接下来添加一篇文章：

```
middleman article new_article
```



这时 source 目录下新增加了文件，打开这个文件，会看到一个 markdown 格式的文章以及一些元信息。









