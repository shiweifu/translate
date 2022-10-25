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





























