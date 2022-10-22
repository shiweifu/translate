# Rails Concerns：该不该使用？



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









