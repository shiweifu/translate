# 该不该使用 Rails Concerns？



如果你使用过 Ruby on Rails，你可能接触过关于 concern 的概念。每当新建一个项目，项目的目录结构中，都会有 `app/controllers/concerns` 和 `app/models/concerns` 两个目录。但，什么是 concerns？为什么 Rails 社区的人们，对他们说他们糟糕？



## 快速预览



一个 Rails 中的 Concern，是一个继承了 `ActiveSupport::Concern` 的模块。你也许会问，一个 `concern` 和普通的 `module` 有什么区别？主要不同是，Rails 中的 concern，允许你做一些魔法，比如：



```
module Trashable
  extend ActiveSupport::Concern

  included do
    scope :existing, -> { where(trashed: false) }
    scope :trashed, -> { where(trashed: true) }
  end

  def trash
    update_attribute :trashed, true
  end
end
```



你看到了 `included` 这个关键字。它是洒在 Ruby 模块上的碳水化合物。`ActiveSupport::Concern` 允许你将代码放在被引入的代码块中。举个例子，你希望从 model 中，提取一些垃圾逻辑。`included`允许你这样做，并可以稍后在 `model` 中导入：



```
class Song < ApplicationRecord
  include Trashable

  has_many :authors

  # ...
end
```



此时该模型变的更加简单和顺手了，对吧？而模型定义时减掉的部分，现在变得可复用，可以在其他模型中进行引用这些内容，而不只是 Song 模型独占。

好吧，事情会变得复杂，我们来一探究竟。



## 一个 Mixin 的经典示例



在我们进一步深入讨论这些问题之前，让我们再解释一下。当你看到 `include SomeModule` 或 `extend AnotherModule` 时，它们被称为`mixins`。



mixin 是一组可以被添加到其他类中的代码，如 [Ruby documentation](https://ruby-doc.org/core-2.2.0/Module.html) 中所说，一个 module 是一组方法或者常量。所以，我们在这里所做的是将带有方法和常量的模块包含到不同的类中，以便它们可以使用它们。



这正是我们在 `Trashable` concern 上所做的。我们提取了关于将 Trash 模型对象模块中的通用逻辑。该模块稍后可以包含在其他地方。所以，`mixin`，是一种设计模式，不止在 Ruby on Rails 中有应用。但是，无论在哪里使用，人们也不全都是喜欢它的，也有厌恶它的。并认为使用它，容易造成失控。

为了更好地理解这一点，我们将讨论使用它们的几个优点和缺点。希望通过这样做，我们能够了解何时或是否使用关注点。



## 我拥有它的全部



当您决定提取某些东西到 concern 中，比如 `Trashable` concern，此时，你可以访问它被引入的任意功能。这带来了强大的力量，但是，`Richard Schneeman` 在 [他的博客中](https://rollout.io/blog/when-to-be-concerned-about-concerns/) 曾说过，“强大的力量，往往伴随着复杂的代码”。他指的是，你可能依赖复杂的代码。



如果我们再次查看 `Trashable`：



```
module Trashable
  extend ActiveSupport::Concern

  included do
    scope :existing, -> { where(trashed: false) }
    scope :trashed, -> { where(trashed: true) }
  end

  def trash
    update_attribute :trashed, true
  end
end
```



concerns 的逻辑依赖于这样一个事实：即无论哪里包含了 concern，都存在 `trashable` 的字段。对吧？没什么大不了的，这毕竟是我们想要的。但是，我所看到的情况是，人们倾向于从模型中引入其他东西到`concern`中。为了描绘出这是如何发生的，让我们想象一下，`Song` 模型有另一种以作者相关的方法：`featured_authors`：



```
class Song < ApplicationRecord
  include Trashable

  has_many :authors

  def featured_authors
    authors.where(featured: true)
  end

  # ...
end

class Album < ApplicationRecord
  include Trashable

  has_many :authors

  def featured_authors
    authors.where(featured: true)
  end

  # ...
end
```



为了更好地说明，我添加了一个 `Album` 模型，它还包括 `Trashable` 。



然后，假设我们想要在 Song 和 Album 的主打作者被丢弃时通知他们，人们会倾向于像这样将这个逻辑放入关注中：



```
module Trashable
  extend ActiveSupport::Concern

  included do
    scope :existing, -> { where(trashed: false) }
    scope :trashed, -> { where(trashed: true) }
  end

  def trash
    update_attribute :trashed, true

    notify(featured_authors)
  end

  def notify(authors)
    # ...
  end
end
```



在这里，事情开始变得有点复杂了。由于我们在Song模型之外有丢弃逻辑，所以我们可能会倾向于将通知放在可丢弃关注点中。在那里，发生了一些“错误”的事情。特色作者取自宋模型。好的，假设它通过了拉请求审查和CI检查。



然后，几个月后，一个新的要求被设定了，开发者需要改变我们呈现歌曲特色作者的方式。例如，一项新要求只想显示来自欧洲的特色作者。自然，开发人员会找到定义特色作者的位置并编辑它们。

















