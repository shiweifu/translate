翻译自：[How to Test Rails Models with RSpec - Semaphore (semaphoreci.com)](https://semaphoreci.com/community/tutorials/how-to-test-rails-models-with-rspec)



测试是我们开发者，在开发过程中，消耗最多时间的部分。[好的测试](https://semaphoreci.com/blog/automated-testing-cicd)，会创造出高质量的软件，减少 bug；长期来看，会使我们的工作更加轻松。



在本文中，我们讨论 Ruby on Rails 中，几个关于测试的基本概念：



- 什么是 BDD
- 如何测试 Rails 中的模型
- 如何通过 Rails 测试业务逻辑
- 如何使用[持续集成](https://semaphoreci.com/continuous-integration)来自动化测试



### 什么是行为驱动开发？



行为驱动开发（behavior -driven Development，BDD）由多个子技术组成。在其内部，它将测试驱动开发（测试驱动开发允许短反馈循环）、领域驱动设计以及面向对象的设计和分析相结合。一个开发流程，它允许涉众和开发人员享受短反馈循环的优点，通过编写适量的代码和设计，使软件可以工作。你可以[在此](https://semaphoreci.com/community/tutorials/behavior-driven-development).，读到更多有关 BDD 的介绍



### 介绍我们的模型



当我们想到 Rails 模型的时候，我们通常想到的是提供解决方案的域。模型有的时候，是具有丰富行为的成熟对象。其他时候，它们只是简单的数据容器或者数据映射。



处于本教程的目的，我们将创建一个名为 `Auction` 的模型。我们将保持简洁，每个 `auction` 将只有一个项目出售。拍卖模式将有一个结束日期，项目标题，项目描述和出售卖家。



让我们创建一个新的 Rails 项目：



```
$ gem install rails rspec
$ rails new --skip-bundle auctions
$ cd auctions
```



添加  [rspec-rails](https://rubygems.org/gems/rspec-rails) 到 Gemfile：



```
# Gemfile

. . .

group :development, :test do
  gem 'rspec-rails', ">= 3.9.0"
end
```



安装 Gem，并完成初始化： 



```
$ bundle install --path vendor/bundle
$ bin/rails generate rspec:install
$ bin/rails webpacker:install 
```



让我们实用 Rails 生成器，来生成模型：



```
$ bin/rails generate model Auction \
                     start_date:datetime \
                     end_date:datetime \
                     title:string \
                     description:text

      invoke  active_record
      create    db/migrate/20160406205337_create_auctions.rb
      create    app/models/auction.rb
      invoke    rspec
      create      spec/models/auction_spec.rb
```



之后，让我们运行迁移和准备数据库：



```
$ rake db:migrate db:test:prepare

== 20160406205337 CreateAuctions: migrating ===================================
-- create_table(:auctions)
   -> 0.0021s
== 20160406205337 CreateAuctions: migrated (0.0023s) ==========================
```



目前，我们忽略了 `bids` 和 `sellers`，聚焦在 `title`，`description`，`start_date` 和 `end_date` 上。



既然已经打好了基础，就开始工作吧。我们要处理的第一件事是验证。



### 指定验证



由于我们目前还不确定应该在模型中进行哪些验证，让我们创建一些未决的示例来开始工作。



编辑名为 `spec/models/auction_spec.rb` 的文件：



```
# spec/models/auction_spec.rb

require 'rails_helper'

RSpec.describe Auction, :type => :model do
  it "is valid with valid attributes"
  it "is not valid without a title"
  it "is not valid without a description"
  it "is not valid without a start_date"
  it "is not valid without a end_date"
end
```



这里我们给出了基本的验证。第一个规范的目的是明确需要什么来创建`Auction`类的有效对象。如果我们运行这个规范，我们将看到五个未决的规范：

```
$ rspec spec/models/auction_spec.rb

*****

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Auction is valid with valid attributes
     # Not yet implemented
     # ./spec/models/auction_spec.rb:6

  2) Auction is not valid without a title
     # Not yet implemented
     # ./spec/models/auction_spec.rb:7

  3) Auction is not valid without a description
     # Not yet implemented
     # ./spec/models/auction_spec.rb:8

  4) Auction is not valid without a start_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:9

  5) Auction is not valid without a end_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:10


Finished in 0.00216 seconds (files took 1.18 seconds to load)
5 examples, 0 failures, 5 pending
```



所有的用例已经准备好，让我们实现第一个。



```
# spec/models/auction_spec.rb

require 'rails_helper'

RSpec.describe Auction, :type => :model do
  it "is valid with valid attributes" do
    expect(Auction.new).to be_valid
  end

  it "is not valid without a title"
  it "is not valid without a description"
  it "is not valid without a start_date"
  it "is not valid without a end_date"
end
```



我们没有给模型添加任何约束，我们的模型对象将在没有指定任何属性的情况下验证有效。我们来执行用例，看看效果。



```
$ rspec spec/models/auction_spec.rb

.****

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Auction is not valid without a title
     # Not yet implemented
     # ./spec/models/auction_spec.rb:8

  2) Auction is not valid without a description
     # Not yet implemented
     # ./spec/models/auction_spec.rb:9

  3) Auction is not valid without a start_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:10

  4) Auction is not valid without a end_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:11


Finished in 0.01073 seconds (files took 1.17 seconds to load)
5 examples, 0 failures, 4 pending
```



接下来，让我们实现第二个例子。在这个例子中，我们将创建一个没有标题的 `Auction` 对象。



```
# spec/models/aution_spec.rb

. . .

it "is not valid without a title" do
  auction = Auction.new(title: nil)
  expect(auction).to_not be_valid
end

. . .
```



如果我们现在运行这个用例，它将失败。



```
$ rspec spec/models/auction_spec.rb

.F***

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Auction is not valid without a description
     # Not yet implemented
     # ./spec/models/auction_spec.rb:13

  2) Auction is not valid without a start_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:14

  3) Auction is not valid without a end_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:15


Failures:

  1) Auction is not valid without a title
     Failure/Error: expect(auction).to_not be_valid
       expected #<Auction id: nil, start_date: nil, end_date: nil, title: nil, description: nil, created_at: nil, updated_at: nil> not to be valid
     # ./spec/models/auction_spec.rb:10:in `block (2 levels) in <top (required)>'

Finished in 0.03033 seconds (files took 1.39 seconds to load)
5 examples, 1 failure, 3 pending

Failed examples:

rspec ./spec/models/auction_spec.rb:8 # Auction is not valid without a title
```



如你所见，这个例子因为我们的验证，需要添加到模型中，所以失败了。由于我们已经完成了红色的步骤（由红到绿的循环），让我们现在完成测试。要实现这个目标，我们需要将标题属性的验证，添加到 `app/models/auction.rb`：



```
# app/models/auction.rb 

class Auction < ActiveRecord::Base
  validates_presence_of :title
  validates_presence_of :description
  validates_presence_of :start_date
  validates_presence_of :end_date
end
```



验证就绪后，我们可以再次执行测试：



```
$ rspec spec/models/auction_spec.rb

F.***

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Auction is not valid without a description
     # Not yet implemented
     # ./spec/models/auction_spec.rb:13

  2) Auction is not valid without a start_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:14

  3) Auction is not valid without a end_date
     # Not yet implemented
     # ./spec/models/auction_spec.rb:15


Failures:

  1) Auction is valid with valid attributes
     Failure/Error: expect(Auction.new).to be_valid
       expected #<Auction id: nil, start_date: nil, end_date: nil, title: nil, description: nil, created_at: nil, updated_at: nil> to be valid, but got errors: Title can't be blank
     # ./spec/models/auction_spec.rb:5:in `block (2 levels) in <top (required)>'

Finished in 0.02039 seconds (files took 1.18 seconds to load)
5 examples, 1 failure, 3 pending

Failed examples:

rspec ./spec/models/auction_spec.rb:4 # Auction is valid with valid attributes
```









