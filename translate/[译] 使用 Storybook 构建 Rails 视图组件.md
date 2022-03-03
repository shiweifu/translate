翻译自：[Building a Component Library in Rails With Storybook - DEV Community](https://dev.to/orbit/building-a-component-library-in-rails-with-storybook-49m4)



最近几年，Rails 生态系统突飞猛进的得到进化，并且得到了使用 JavaScript 框架的开发人员的喜爱。



在代号为 NEW MAGIC（现在被称为  [Hotwire](http://hotwire.dev/)）的背后，Basecamp 团队在 2020 年，发布了  [Turbo](https://turbo.hotwire.dev/)  和 [Stimulus](https://stimulus.hotwire.dev/)，添加强大的功能，如近即时导航、第一方 WebSocket 支持、应用程序的延迟加载，以及许多其他功能。



然而，我最感兴趣的使用 Rails 开发的部分，是构建自己的组件库的能力，由 [View Component](https://viewcomponent.org/) 和 [Storybook](https://storybook.js.org/) 提供支持。



组件库是一组可以在整个应用中重用的组件（buttons、alert、特定领域的小部件等），减少了重复的构建，并改善了我们的用户体验和代码库的一致性。



本文将介绍如何构建你自己的视图组件库，并将其与Storybook一起部署，团队的所有成员能够单独尝试、调整和检查它们。



#### ViewComponents 和 Storyboard 入门



去年秋天，我偶然发现了 Joel Hawksley在 RailsConf上 发表的一个名为“ [Encapsulating Views](https://railsconf.org/2020/2020/video/joel-hawksley-encapsulating-views)”的演讲，介绍了视图组件gem GitHub如何实现 Rails 中的类似 react 的组件。



视图组件使得在你的Ruby on Rails应用程序中构建可重用、可测试和封装的组件变得很容易。我强烈建议在继续解释文档的好处和用例之前，先看一看文档的前几段。



在Orbit，我们正在慢慢地构建一个视图组件列表，我们可以在应用程序的按钮、选择、下拉菜单中重用这些组件。然而，随着列表的增长，整个团队(工程、设计和产品)越来越难知道哪些组件已经可用，哪些组件可以重用。我们需要一种方法来组织这个库。



在基于js的应用程序中，有一个常见的(而且真的很神奇的)组件库工具叫做 [Storybook](https://storybook.js.org/)。Storybook 是一个通过响应式的操作，来构建 UI 组件，以及组件的文档和其他细节。这里有一些 Storybook 的例子：

- 这个例子来自 [The Guardian](https://5dfcbf3012392c0020e7140b-gmgigeoguh.chromatic.com/?path=/story/layouts-immersive--article-story)
- 这个例子来自 [GitLab](https://gitlab-org.gitlab.io/gitlab-ui/?path=/story/base-broadcast-message--default)
- 这个例子来自 [Shopify](https://5d559397bae39100201eedc1-nqqiwjtuqe.chromatic.com/?path=/story/all-components-skeleton-page--all-examples)
- 还有我们自己 [Orbit](https://app.orbit.love/_storybook/index.html)



Storybook 只用于使用 JavaScript 框架创建的单页面 Web 应用：React，Vue，Angular，以及其他一些。幸运的是，最新的 V6 版本，引入了 [@storybook/server](https://github.com/storybookjs/storybook/tree/master/app/server) 包，允许任意的 HTML 代码使用 Storybook 组件。理论上，这允许 Rails 后端呈现 Storybook 的组件。但这在实践中是如何运作的呢？

























