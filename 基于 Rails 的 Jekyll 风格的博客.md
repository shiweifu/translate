# 基于 Rails 的 Jekyll 风格的博客



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





