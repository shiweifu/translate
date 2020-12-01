# 基于 Rails 的 Jekyll 风格的博客



翻译自：[Jekyll-Style Blogging On Rails - Ken Collins @MetaSkills.net](https://metaskills.net/2014/04/19/jekyll-style-blogging-on-rail/)



是否想在新博客中使用已经存在的 Rails 布局和业务逻辑？是否喜欢 Jekyll，但是想让两者进行结合？



本着做具备可用性的，最简单工作的精神，除了尽可能的模仿 Jeykll 之外，我的要求还被定义为：



- 使用 `YYYY-MM-DD-blog-post-title.md` 作为日志的文件命名
- 支持代码语法高亮
- 为资源和合集，暴露模型类查找器
- 一些模型对象询问标题，日期，html，以及其他
- 开发逻辑符合 Rails 的习惯。像页面刷新一样
- 在生产环境提供缓存



如果我还有时间，我将额外做以下工作：

- 使用 YAML 格式头部来配置日志的标题和其他信息
- 使用 `ERB` 来预处理 `markdown`，并允许使用 `Rails` 的路由 `helpers`
- 获取每个日志的摘录信息
- 创建 `rake` 任务，使用当前日期创建新日志



### 开始



多数情况下，我喜欢从构建高级接口开始编写代码。这帮助我考虑以后将要构建的一些细节。一个 Rails 控制器是一个不错的选择，但是要小心，并保持简单。如果你是喜欢先看最后结果的那种人，请查看我们工作最后的 gist。



首先，我们构建我们的日志路由和控制器。我们想要两个 `action`，一个 `index`，来列出全部的博客日志，以及 `show`，来查看日志。



```
# In config/routes.rb
resources :blog, only: [:index, :show]

# In app/controllers/blog_controller.rb
class BlogController < ApplicationController

  layout 'site'
  before_filter :find_post, only: [:show]

  def index
    @posts = BlogPost.all
  end

  def show
    render text: @post.html, layout: 'site' if stale? @post, public: true
  end

  private

  def find_post
    @post = BlogPost.find params[:id]
    redirect_to blog_index_path unless @post
  end

end
```



这段代码看上去非常简单，但包含了很多细节，我们在做很多工作的时候，都需要构建这些细节。 我们的 `index` action 展示了我们将需要一个用于所有帖子的查找方法，并将返回结果排序。`show` `action` 的前面，有一层 `find_post` 过滤器，我们的帖子需要某种类型的 `key`，同样，我们已经确定我们需要 `html` 实例方法。最后，我们将通过帖子的 `Cache-Control` 标头，检查帖子是否过时。



### 我们的日志模型



我们的 `:id` 键，将使用文件名 。控制器在 `app/views/blog` 目录下查找我们的 `markdown` 文件。在此创建第一篇日志，使用当前日期作为文件名 `2014-04-19-my-first-post.md`。现在，这是我们对 `BlogPost` 模型的一个测试，它使我们能够找到更多的帖子。



```
class BlogPost

  attr_reader :slug

  class << self

    def all
      all_slugs.map{ |slug| new(slug) }.sort
    end

    def find(slug)
      all.detect { |post| post.slug == slug }
    end

    def directory
      Rails.root.join 'app', 'views', 'blog'
    end

    private

    def all_slugs
      @all_slugs ||= Dir.glob("#{directory}/*.md").map { |f| File.basename(f).sub(/\.md$/,'') }
    end

  end

  def initialize(slug)
    @slug = slug
  end

end
```



需要注意的是，私有类方法 `all_slugs`，在生产环境中，一次一次的扫描文件系统？这种做法并不是很好，我们后续可以根据需要，来更新这个方法。所以，除了类方法之外，我们可以刷新模型之外的东西。我们现在先来讨论 `Jeykll`。



### Jeykll相关



我现在使用 Jeykll 用于静态网站和博客。大部分情况下，我都使用 Jeykll 暂时未正式发布的下一个大版本。Jeykll v2 很棒，我们在本项目中使用它。我们需要使用 bundle 来安装它。别忘了，当 Jeykll v2 正式发布后，更新 Gemfile 文件的引用为正式版本。



```
# In Gemfile
gem 'jekyll', '~> 2.0.0.alpha'
gem 'redcarpet'
gem 'rouge'
```



通常，我们都很熟悉 `Redcarpet` gem。它用于将 markdown 转换为我们想要的格式，但 `rouge` 是什么东东？ `Rouge` gem 是纯 ruby 实现的语法高亮组件。它可以高亮超过 60 种语言，并输出 HTML 或者 ANSI 256-color 文本。它输出的 HTML，兼容已经存在的为 `pygments` 设计的样式。



```
class BlogPost

  # ...

  private

  def to_html
    Jekyll::Converters::Markdown::RedcarpetParser.new({
      'highlighter' => 'rouge',
      'redcarpet' => {
        'extensions' => [
          "no_intra_emphasis", "fenced_code_blocks", "autolink",
          "strikethrough", "lax_spacing",  "superscript", "with_toc_data"
        ]
      }
    }).convert(markdown)
  end

end
```



不止是看起来很棒，`Rouge` 的执行速度也非常快！通过使用它，我们避免绑定 pygments，其具有 Python 依赖。我们将让 `Jekyll` 努力工作，并实现我们的私有 `to_html` 方法。这可以让我们的 Github 风格的 markdown，支持语法高亮。





