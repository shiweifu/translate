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









