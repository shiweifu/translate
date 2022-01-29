翻译自：[What's New in Rails 7 | AppSignal Blog](https://blog.appsignal.com/2021/12/15/whats-new-in-rails7.html)



Rails 7就在眼前。我们还没有确定的发布日期，但预计将在圣诞节前推出，所以不会太久。本文发布的最新版本是7.0.0。Rc1，第一个发布候选版本。Basecamp, HEY, Github和Shopify都已经在生产环境中运行了Rails 7 alpha版本，所以我们可以预期，即使是发布候选版本也会相当稳定。



在这篇文章中，我们将看看Rails 7将带来的一些新特性和变化。



#### Node 和 Webpack 不再是必须的



是的，你没看错!Rails 7中的JavaScript将不再需要NodeJS或Webpack。你仍然可以使用npm包。



用Babel编译ES6和用Webpack打包需要大量的设置。虽然Rails用Webpacker的gem很好地支持了它，但这带来了很多包袱，很难理解和修改，尤其是在保持可升级性的情况下。