Pagy 是一个用于分页的 Gem。



在 Rails 生态下，最有名的分页 Gem 是 [kaminari/kaminari: ⚡ A Scope & Engine based, clean, powerful, customizable and sophisticated paginator for Ruby webapps (github.com)](https://github.com/kaminari/kaminari)。功能强大，接口简单，支持多种 ORM，基本开箱即用。



但使用上，有侵入性，性能一般。



Pagy 2017 年才发布，作者认为，分页是一项简单的任务，应该避免复杂化，该项目也追求简单明了：

- 不需要学习额外的 DSL
- 不绝对避免全局变量
- 不需要任何特殊模块或者适配器
- 不使用嵌套的模块、类



项目分为三部分：

- 分页的核心：[pagy.rb](https://github.com/ddnexus/pagy/blob/master/lib/pagy.rb)
- 后端需要引入的内容：[pagy/backend.rb](https://github.com/ddnexus/pagy/blob/master/lib/pagy/backend.rb)
- 前端需要引入的内容：[pagy/frontend.rb](https://github.com/ddnexus/pagy/blob/master/lib/pagy/frontend.rb)



此外，相对于 Kaminari，Pagy 还有非常可观的性能提升， Github 首页一打开，就是一张性能对比图：



![IPS Chart](https://github.com/ddnexus/pagy/raw/master/docs/assets/images/ips-chart.png)



按官方的说法，性能提升了 40 倍……



#### 安装



Gemfile 文件中，添加：

```
gem 'pagy', '~> 5.10'
```

接着使用 `bundle install` 安装即可。

如果不使用 bundle 进行包管理，手动安装：

```
gem install pagy
```

然后在 Web 应用执行入口，引入 `pagy`：

`require 'pagy'`。



下面以 Rails 环境下，来介绍 Pagy 的使用。



#### 使用



首先，在 `ApplicationController` 中，引入 Pagy 后端模块：

```
include Pagy::Backend
```

这句话引入了 `pagy` 方法，接下来就可以在需要分页的 action 中，调用 pagy 方法，进行分页了：



```
 @pagy, @records = pagy(Product.some_scope)
```



Product 是要进行分页的对象，some_scope 可以是用户自定义的 scope，也可以是模型自带的 scope，如 `Product.all`。



注意用到的 `scope`，不要附带 `limit`，如果带了 `limit`，会导致分页异常。



#### 配置默认参数



Rails 应用在加载阶段，会加载 `config/initializers` 目录下的文件，项目所用到的第三方库初始化一般放在这个地方。Pagy 的配置，是通过一系列全局变量来进行配置的，也可以放在这里。















