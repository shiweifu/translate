翻译自：[Using Hanami after a decade building Rails apps - DEV Community](https://dev.to/mculp/using-hanami-after-a-decade-building-rails-apps-4jnl)



我清楚的记得，第一次看到 DHH 的博客中，15 分钟的视频时我在哪里。那时我总是使用 PHP 开发我的项目，而那个视频令我印象深刻。



![image-20210921000215004](C:\Users\shiweifu\AppData\Roaming\Typora\typora-user-images\image-20210921000215004.png)



我立刻对 Ruby on Rails 充满了幻想。接下来的两年，我主要工作是使用 Java，业余时间我都在学习 Ruby on Rails。最终，在我学习到了足够多的内容后，我的职业生涯从 Java 跳转到 Rails，接下来的十年，我都在致力于 Rails 程序开发。



最近，我有一个业余项目需要开发，我决定使用 Hanami 替代 Rails，来构建它。我选择使用 Hanami，有以下几个理由：



1. 我有了额外的可以自由支配的时间，来学习新东西
2. 我的 Rails 应用，最近趋向于使用大量小规模的的服务对象和 PORO 构建
3. Hanami 的应用架构，像是从 Rails 发展而来



接下来，本文将为你展示一些我所了解的 Hanami，以及我喜欢的部分。



#### 项目



密西西比周，每日提供表格，其中包含每日新增的 COVID 病例，以及死亡人数。



![MSDH table](https://res.cloudinary.com/practicaldev/image/fetch/s--al45FuLW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/s2jy9y2g2nthtfpxlu4n.png)



这张表格图中，只展示了每日增加的人数以及死亡的人数，你无法在其中感受到各个地区的数据的增长。



我决定爬取这个表格，并且减去昨日的数据，以获得每日最新的数字。



![Screenshot of my project](https://res.cloudinary.com/practicaldev/image/fetch/s--keo1WzLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/whapy4597ijxpvuaxrwj.png)



我也会保存这些数据，在历史视图中展示。



### Harrison



![img](https://res.cloudinary.com/practicaldev/image/fetch/s--cPMlDk5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/t751mqsagso4cj3x3yhr.png)



我存储地区名字，在 `countries` 表，我还有一个表，叫做 `country_updates`，这个表的字段如下：



- `cases`
- `deaths`
- `ltc_detahs`
- `ltc_cases`
- `country_id`
- `previous_update_id`，这是一个自引用的外键



#### 实现



##### Repositories 和 Entities



Hanami 将 Rails 中的模型，拆分为 `repository` 类，和 `entity` 类。`repository` 类用于数据库的查询操作，而 `entity` 类，用于展示你的数据。`entity` 类自动与你的表结构进行映射。 



我添加了 `CountryUpdate` entity，在自动映射的字段上方，增加了一些需要计算的方法。



```
class CountyUpdate < Hanami::Entity
  def new_cases
    return unless previous_update

    cases - previous_update.cases
  end

  def new_deaths
    return unless previous_update

    deaths - previous_update.deaths
  end

  def new_ltc_cases
    return unless previous_update

    ltc_cases - previous_update.ltc_cases
  end

  def new_ltc_deaths
    return unless previous_update

    ltc_deaths - previous_update.ltc_deaths
  end

  def new_cases_percent_change
    return unless previous_update

    (new_cases.to_f / previous_update.cases.abs * 100).round(1)
  end
end
```



这种写法的一个优点是，你的查询是独立的，而且易于测试。



```
RSpec.describe CountyUpdateRepository, type: :repository do
  let(:repo) { CountyUpdateRepository.new }

  describe '#find_latest_by_county_id' do
    it 'finds the latest update for a county' do
      build_stubbed(:county, :with_updates)

      latest_date = repo.find_latest_by_county_id(1).date.to_date
      expect(latest_date).to eq Date.today
    end
  end
end
```



#### 控制器层



在 Hanami 中，每个路由对应一个 Action 类。这与 Rails 形成对比，在 Rails 中，每个路由，对应 Controller 类中的一个方法。



在我的应用中，我写了一条路由，对应列出密西西比的城镇。



```
get '/counties', to: 'counties#index'
```



当访问`/countries`时，对应的类被调用。



```
module Web
  module Controllers
    module Counties
      class Index
        include Web::Action

        expose :counties

        def call(params)
          @counties = CountyWithLatestUpdateRepository.new.all
        end
      end
    end
  end
end
```



同样的，对于 `show` 路由，也有一个相应的 `Show` 操作。



```
module Web
  module Controllers
    module Counties
      class Show
        include Web::Action

        expose :county

        def call(params)
          @county = CountyRepository.new.find_by_name_with_updates(params[:name])
        end
      end
    end
  end
end
```



我真的真的喜欢这一个特性。



Rails 的控制器代码，经常变的杂乱无章，并且包含仅与某些操作相关的无关代码。举个例子，为什么代码中，操作 `index` 的类和操作 `create` 的类中，包含相同的参数？



`Hanami` 的方法遵循单一职责原则。你可以放心，该类中的任何代码，仅与该路由有关。它使您的操作变得非常容易测试。



### Apps



Hanami 的另外一个我非常喜爱的特点是你可以拥有多个 apps，用于你的业务逻辑以不同的方式展示。你的业务逻辑存在 `lib` 目录，你可以在多个位于 `app` 目录的 应用中。当你创建一个新的 Hanami 应用，你将获得一个位于 `app/web` 中的服务端渲染应用。



当我需要创建第二个应用程序的时候，我才完全理解多应用有多棒。在 Twitter 上与某人进行了短暂交流后，我决定通过生成一个新的提供 API 的 App 来实现这个功能。



我使用 `hanami` 命令来生成新的 app，保存在 `app/api`：



```
bundle exec hanami generate app api
```



然后，我只是简单增加一个控制器，用于渲染 JSON。



```
module Api
  module Controllers
    module Counties
      class Show
        include Api::Action

        def call(params)
          county = CountyRepository.new.find_by_name_with_updates(params[:name])
          self.body = JSON.dump(county.to_h)
          self.format = :json
        end
      end
    end
  end
end
```



一分钟之内，我就完成了一个功能性的 API。



与 Rails 不同，这个 API 是纯粹的额外代码。我不必接触任何现有代码，来处理不同的内容类型。我也不必编写 `respond_to` 块。如果我愿意，我可以对我的 JSON 和 HTML 响应使用两个不同的查询，而无需分支逻辑。



#### Takeaways



我从 Hanami 中的最大的收获是，代码质量相较于使用 Rails 开发项目时，有所提高。



我已经使用了 Rails 超过十年，有时我仍然在努力弄清楚我应该把代码放在哪里。”这个代码属于模型吗？“我应该在什么时候将模型代码移动到服务对象？”我在Rails项目中遇到过这种情况。



我在 Hanami 中还没有遇到过这样的问题。它几乎让人感觉框架加强了代码质量。这让编写糟糕的代码变得更加困难。



这里并没有太多的魔法。这个框架是用来指导你的，而不是让你惊讶。



我对这个项目的结果印象深刻。在某些情况下会有一点学习曲线，但我能够克服这个障碍。“Hanami 2.0”正在开发中，我很期待它能带来什么。





