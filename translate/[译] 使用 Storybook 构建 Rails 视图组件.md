翻译自：[Building a Component Library in Rails With Storybook - DEV Community](https://dev.to/orbit/building-a-component-library-in-rails-with-storybook-49m4)



最近几年，Rails 生态系统突飞猛进的得到进化，并且得到了使用 JavaScript 框架的开发人员的喜爱。



在代号为 NEW MAGIC（现在被称为  [Hotwire](http://hotwire.dev/)）的背后，Basecamp 团队在 2020 年，发布了  [Turbo](https://turbo.hotwire.dev/)  和 [Stimulus](https://stimulus.hotwire.dev/)，添加强大的功能，如近即时导航、第一方 WebSocket 支持、应用程序的延迟加载，以及许多其他功能。



然而，我最感兴趣的使用 Rails 开发的部分，是构建自己的组件库的能力，由 [View Component](https://viewcomponent.org/) 和 [Storybook](https://storybook.js.org/) 提供支持。



组件库是一组可以在整个应用中重用的组件（buttons、alert、特定领域的小部件等），减少了重复的构建，并改善了我们的用户体验和代码库的一致性。











