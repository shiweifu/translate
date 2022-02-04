翻译自：[What's New in Rails 7 | AppSignal Blog](https://blog.appsignal.com/2021/12/15/whats-new-in-rails7.html)



Rails 7就在眼前。我们还没有确定的发布日期，但预计将在圣诞节前推出，所以不会太久。本文发布的最新版本是7.0.0。Rc1，第一个发布候选版本。Basecamp, HEY, Github和Shopify都已经在生产环境中运行了Rails 7 alpha版本，所以我们可以预期，即使是发布候选版本也会相当稳定。



在这篇文章中，我们将看看Rails 7将带来的一些新特性和变化。



#### Node 和 Webpack 不再是必须的



是的，你没看错!Rails 7中的JavaScript将不再需要NodeJS或Webpack。你仍然可以使用npm包。



用Babel编译ES6和用Webpack打包需要大量的设置。虽然Rails用Webpacker的gem很好地支持了它，但这带来了很多包袱，很难理解和修改，尤其是在保持可升级性的情况下。



现在，使用 `rails new` 命令，默认创建的 app，通过 [`importmaps-rails` gem](https://github.com/rails/importmap-rails/) 来代替 `package.json` 文件，实现包引入以及 通过 `npm` 或者 `yarn` 来安装前端依赖库。现在，你可以使用 `./bin/importmap` 终端命令，来 pin、unpin 以及 update 来维护依赖。

举个例子，来安装 `date-fns`：



```
$ ./bin/importmap pin date-fns
```



这会在 `config/importmap.rb` 文件，添加一行：

```
pin "date-fns", to: "https://ga.jspm.io/npm:date-fns@2.27.0/esm/index.js"
```



在你的JavaScript代码中，你可以像以前一样继续使用所有的东西：



```js
import { formatDistance, subDays } from 'date-fns'
2
3formatDistance(subDays(new Date(), 3), new Date(), { addSuffix: true })
4//=> "3 days ago"
```



在这个设置中需要记住的一点是，您编写的内容和浏览器获取的内容之间没有转换。大多数情况下，这是可以的，因为所有重要的浏览器现在都支持ES6开箱即用。



但这也意味着你不能使用TypeScript或JSX，因为它们需要在使用之前翻译成JS。



因此，如果要使用JSX的React，您仍然必须返回其他设置（使用WebPack / Rollup / ESBuild）。



Rails 7可以为您做到这一点。你所需要的只是一个命令与你选择的策略：

```
$ ./bin/rails javascript:install:[esbuild|rollup|webpack]
```



#### Turbolinks 和 UJS 被替换为 Turbo 和 Stimulus



用Rails 7生成的应用程序默认会得到Turbo和Stimulus(从Hotwire)，而不是Turbolinks和UJS。Hotwire是一种通过网络发送HTML来快速更新DOM的新方法。



#### 数据库层加密



Rails 7允许使用ActiveRecord::Base上的encrypts方法将某些数据库字段标记为加密的。这意味着在初始设置之后，您可以编写如下代码：



```
class Message < ApplicationRecord
  encrypts :text
end
```



您可以像使用其他属性一样继续使用加密的属性。Rails 7将在数据库和应用程序之间自动对其进行加密和解密。



但是这有一点奇怪:您不能通过该字段查询数据库，除非您将一个确定性的:true选项传递给encrypts方法。确定性模式比默认的非确定性模式更不安全，所以只在绝对需要查询的属性时使用它。



#### 异步查询

现在有一个加载异步方法，你可以使用它在后台查询数据获取结果。当您需要从控制器操作加载几个不相关的查询时，这一点尤其重要。可以执行以下命令：



```
def PostsController
  def index
    @posts = Post.load_async
    @categories = Category.load_async
  end
end
```



<<<<<<< HEAD
这将同时在后台触发两个查询。因此，如果每个查询花费200ms，那么获取数据的总时间是~200ms，而不是400ms(如果是连续获取数据的话)。



### Rails 7 中的 Zeitwerk 模式



对于仍然运行经典加载器的旧应用程序来说，这是一个突破性的改变。所有Rails 7应用程序都必须使用Zeitwerk模式，但是切换非常简单。查看完整的Zeitwerk升级指南。



#### 其他 Rails 7 升级



##### 任务无限重试



ActiveJob现在允许传入:unlimited作为重试时的尝试参数。Rails将在没有任何最大尝试次数的情况下继续尝试该作业。



```
class MyJob < ActiveJob::Base
  retry_on(AlwaysRetryException, attempts: :unlimited)

  def perform
    raise "KABOOM"
  end
end
```



#### 名称变动



您现在可以在ActiveStorage上命名变量，而不是在每次访问时指定大小。



```
class User < ApplicationRecord
  has_one_attached :avatar do |attachable|
    attachable.variant :thumb, resize: "100x100"
  end
end

#Call avatar.variant(:thumb) to get a thumb variant of an avatar:
<%= image_tag user.avatar.variant(:thumb) %>
```



#### Hash 转换为 HTML 标签

有一个新的tag.Attributes方法，用于将哈希转换为HTML属性的视图：

```erb
<input <%= tag.attributes(type: :text, aria: { label: "Search" }) %>>
```



将转换为：

```html
<input type="text" aria-label="Search">
```



#### Ruby `debug`

新的调试默认值已经从 byebug 更改为 debug gem。

现在，您需要调用代码中的 debugger 来进入调试会话，而不是调用 byebug。



#### 断言一个单一的记录与sole



在查询记录时，当您想断言查询应该只匹配单个记录时，现在可以调用sole或find sole by(而不是first或find by)。



```
Product.where(["price = %?", price]).sole
# => ActiveRecord::RecordNotFound      (if no Product with given price)
# => #<Product ...>                    (if one Product with given price)
# => ActiveRecord::SoleRecordExceeded  (if more than one Product with given price)

user.api_keys.find_sole_by(key: key)
# as above
```



#### 检查是否存在关联

```
我们现在可以使用where.associated(:association)来检查一个关联是否存在于记录中，而不是连接并检查id是否存在。
```















=======
>>>>>>> fc53284d5dcfda4bf0c225ff6fcb69710bcd48ef












