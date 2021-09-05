翻译自：[Hanami: Thoughts of a Rails developer - DEV Community](https://dev.to/bajena/hanami-thoughts-of-a-rails-developer-ik1)



#### 引子



我还记得第一次听说 Hanami 框架的时候，是几年前，在 Wroclove.rb 会议的时候。当时并没有真正引起我的关注，那时我刚刚进入 Ruby 世界，正 100% 专注于学习 Rails，我不想在大脑认知上，接收另一个框架的信息。



现在，我已经使用 Rails 工作了好几年，我有点感到疲惫了，我认为现在是接触全新概念的时机。我想起 wroclove.rb 的谈话，我决定学习 Hanami。我最喜欢的学习方式是边做边学，所以我开始了一个名为 flashcard-genius 的玩具项目。这个应用的目的是帮助我学习意大利语单词，它允许用户创建、共享和记忆多组卡片。



接下来大概两个月的业余时间，我开发了这个原型产品（已经上线：[http://flashcard-genius.com](http://flashcard-genius.com/)）。它使用了基本的的服务端渲染以及 CRUD 操作，它迫使我学习了各种各样的 Hanami 概念，并克服了多个使用过程中所遇到的障碍。



在本文，我将与你分享我对于 Hanami 的一些印象。



![image](https://res.cloudinary.com/practicaldev/image/fetch/s--aXd-3Uex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/5732023/97406654-d7b7c380-18f9-11eb-9256-89dac0360af7.png)



#### 为什么要使用 Hanami？



Hanami 的作者 Luga Guidi，希望可以去 Rails 化。他的主要愿景是构建一个安全的，功能丰富的 Web 框架，这个框架的结构遵循简洁的架构原则，并依赖于经过实战测试的第三方库，如 Sequel，Rack，以及 dry-rb 这些。



听起来很吸引人，对吧？对我来说，确实如此，特别是因为我们钟爱的 Rails 常常被批评变得过于复杂，以及破坏了一些最佳实践（例如混合了域和数据层）。



内存使用率也可能是 Hanami 的一个潜在好处。Hanami 可能消耗更少的内存，并且速度更快（甚至五倍）(https://rpanachi.com/2016/03/28/from-rails-to-hanami-part1-container-architecture-model-views-assets)。



### 我喜欢什么



#### 初始化



初始化 Hanami 应用非常简单。 [开始](https://guides.hanamirb.org/introduction/getting-started/) 页面解释了该如何做：



- 设置项目
- 创建第一个路由，并链接与之匹配的控制器行为，视图和模板
- 准备基本的测试



良好的第一印象对我非常重要，因为可以直观的看到事物的变化，使我有兴趣继续了解更复杂的概念，来构建我的 app。



### Hanami 项目是一个 “微小的”



https://guides.hanamirb.org/architecture/overview/



Hanami 可以允许存在多个 App 在一个项目中，这有助于项目大了之后的解构和拆分。同样的，每个项目有自己独立的 “lib” 文件夹。



#### 实体、存储库、验证对象和交互响应器替代了胖数据模型



Hanami 尽力阻止开发者在使用 Hanami 中，犯在使用 Rails 中常犯的错误。



- 相较于在模型中，直接使用数据库查询方法，Hanami 提供了存储类。刚开始使用的时候，似乎有点多此一举，但以后你会感受到她的好处。
- 不使用不方便进行测试的生命周期回调（after_create，after_build 等等），Hanami 鼓励您使用被称为交互器的服务对象。
- Hanami 中的 模型，默认只提供相应表的只读访问。相较于 active record 模型，默认就创建大量的模型方法（如 username，username_changed，username_did_change? 等），这是个十分轻量级的实现。
- Hanami 的创造者们，得出结论，验证器不应该成为模型的一部分，因为数据的验证高度依赖用例。因此，他们没有在模型上提供验证机制，而是创建了 `Hanami::Validation` mixin（基于一个超棒的 Gem，`dry-validation`）。Hanami 中的控制器响应事件开箱即用，然而你也可以根据你的需要，创建你自己的验证器对象。如果你想了解更多为什么验证器是一种反模式，我建议你看看 Piotr Solnica 的这个 [视频](https://www.youtube.com/watch?v=nOUPIa7tWpA)。

