翻译自：[Hanami: Thoughts of a Rails developer - DEV Community](https://dev.to/bajena/hanami-thoughts-of-a-rails-developer-ik1)



#### 引子



我还记得第一次听说 Hanami 框架的时候，是几年前，在 Wroclove.rb 会议的时候。当时并没有真正引起我的关注，那时我刚刚进入 Ruby 世界，正 100% 专注于学习 Rails，我不想在大脑认知上，接收另一个框架的信息。



现在，我已经使用 Rails 工作了好几年，我有点感到疲惫了，我认为现在是接触全新概念的时机。我想起 wroclove.rb 的谈话，我决定学习 Hanami。我最喜欢的学习方式是边做边学，所以我开始了一个名为 flashcard-genius 的玩具项目。这个应用的目的是帮助我学习意大利语单词，它允许用户创建、共享和记忆多组卡片。



接下来大概两个月的业余时间，我开发了这个原型产品（已经上线：[http://flashcard-genius.com](http://flashcard-genius.com/)）。它使用了基本的的服务端渲染以及 CRUD 操作，它迫使我学习了各种各样的 Hanami 概念，并克服了多个使用过程中所遇到的障碍。



在本文，我将与你分享我对于 Hanami 的一些印象。



![image](https://res.cloudinary.com/practicaldev/image/fetch/s--aXd-3Uex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/5732023/97406654-d7b7c380-18f9-11eb-9256-89dac0360af7.png)



#### 为什么要使用 Hanami？



Hanami 的作者 Luga Guidi，希望可以去 Rails 化。他的主要愿景是构建一个安全的，功能丰富的 Web 框架，这个框架的结构遵循简洁的架构原则，并依赖于经过实战测试的第三方库，如 Sequel，Rack，以及 dry-rb 这些。





