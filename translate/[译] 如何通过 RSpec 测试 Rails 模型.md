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

