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



### 其他 BLOGSPOT 的细节



在 `BlogPost.all` 类方法中，我们对合集进行了排序，期待最新的帖子在最前面。我们通过 Ruby 的 `<=>` 方法，比较每个帖子的日期，来实现这一功能。这完成了我们控制器的索引需求。



````
class BlogPost
  # ...
  def <=> other
    other.date <=> date
  end
  # ...
end
````



我们希望每个帖子都有 `title` 和 `date` 方法。可以通过简单的解析帖子的 `slug` 来获取。我同样增加了 `date_formatted` 方法，使用 `ActiveSupport` 的 `Inflector` 来在我们的视图中进行调用。它返回格式化的字符串，类似 `Arpil 19th, 2014`。我们也有一个 `path` 方法，当我们的网站中的内容需要链接我们的日志时，使用它。该方法使用 `slug` 作为 `:id` 参数。通过该方法，完成了我们控制器的 `find_post` 功能，并提供了类似 Jeykll 漂亮的 URL。



```
class BlogPost
  # ...
  def title
    slug.sub(/\d{4}-\d{2}-\d{2}-/, '').titleize
  end

  def date
    Date.parse(slug)
  end

  def date_formatted
    day_format = ActiveSupport::Inflector.ordinalize(date.day)
    date.strftime "%B #{day_format}, %G"
  end

  def path
    "/blog/#{slug}"
  end
  # ...
end
```



在实现我们的 markdown 转换 HTML 时，我们应该考虑缓存。根据 Rails 的约定，我们实现 `cache_key` 方法，该方法将博客名称空间与帖子唯一的 `slug` 和 `timestamp` 结合在一起。 `updated_at` 时间戳，只是标识文件在磁盘上的最后一次修改。通常，将转换为自 `unix epoch` 开始的秒数的整数。确定响应 `ETag`，控制器的 `show` 方法，使用 `cache_key`。



 ```
class BlogPost
  # ...
  def updated_at
    File.mtime(file_path)
  end

  def cache_key
    ActiveSupport::Cache.expand_cache_key ['blog', slug, updated_at.to_i]
  end
  # ...
end
 ```



现在到了有趣的部分，转换 `markdown` 为 `HTML`。我们仍然使用 `Rails.cache`，它在本地开发中，使用简单的内存存储，在生产环境中是 `memcached`。`fetch` 方法需要一个密钥和一个回调函数。仅当高速缓存未命中时才执行该块。



```
class BlogPost
  # ...
  def html
    Rails.cache.fetch("#{cache_key}/html") { to_html }
  end


  private

  def file_path
    self.class.directory.join "#{slug}.md"
  end

  def markdown
    Rails.cache.fetch("#{cache_key}/markdown") { File.read(file_path) }
  end
  # ...
end
```



### 代码高亮



只剩下一件事，就是对 `Rouge` gem 输出的内容进行语法高亮适配。幸运的是，互联网上已经存在许多 `pygment` 主题。下面是其中能找到的一部分。



- [pygments-css](https://github.com/richleland/pygments-css) - 从 `pygment` 内联样式创建的 CSS 文件
- [GitHub-Dark](https://github.com/StylishThemes/GitHub-Dark/) - GitHub 暗黑风格，用于 `Stylish`。请浏览主题目录
- [pygments-github-style](https://github.com/aahan/pygments-github-style) - 用于 `Pygments` 的GitHub 样式 



对于 [HomeMarks](http://homemarks.com/) 的博客，我使用上面最后一个资源中的 github `jeykll-github.css`。自由的选择自己项目，然后以自己项目相匹配的方式，将其 `@import` 到现有的 CSS 包中。



### 哪里可以改进？



到此为止，我们已经跑起来这个项目了！但像 `YAML` 顶部格式问题，`ERB` 模板，`Rails` 路由/helpers，以及其他问题该如何解决呢？好消息是我们有时间做所有这些事情。你可以在[Jekyll-Style Blogging On Rails](https://gist.github.com/metaskills/11071934) 这个 gist 中找到这些实现。



```
def scope
  ApplicationController.helpers.clone.tap do |h|
    h.singleton_class.send :include, Rails.application.routes.url_helpers
  end
end
```



我特别得以的一个地方，是通过对 Rails 的路由和帮助方法的评估，来实现的对 `ERB` 的预处理。在深入挖掘 `IRB console` 帮助方法和 `ActionPack` 后，变得非常简单。我上面的解决方案在 Tilt 的 erubis 模板中，通过 `scope` 关键字进行使用。



### 接下来的有哪些事？



通过 [初始化](https://gist.github.com/metaskills/11071934)，对于 Jeykll On Rails，你有了一个很好的构建基础。根据你的需要随意去定制吧。下面是一些主意：



- 在生产环境中，更好的缓存处理。当前使用 `cache_key` 来命中文件系统。
- 也许可以构建一个 `jeykll-rails` 引擎？
- 为你的日志，增加 emoji HTML 过滤器



### 资源



感谢阅读至此！要是能听到你的反馈就更好啦。



下面是一些资源：

- [The Final "Jekyll-Style Blogging On Rails" Gist](https://gist.github.com/metaskills/11071934)

- [Do Simple Things](http://c2.com/cgi/wiki?DoSimpleThings)