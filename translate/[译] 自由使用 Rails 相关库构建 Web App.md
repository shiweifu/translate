翻译自：https://shopify.engineering/building-web-app-ruby-rails



Ruby on Rails  是一个包含许多类库的 Web 框架，依赖这些类库，你可以创建以及分发 Web 应用。通常，我们使用 `rails new` 命令，以创建功能完整、包含大量特性的的 Web 应用程序。对我们大部分人来说，这已经足够了，Rails 的目标是使用魔法使用一切正常运行，而无需知道背后发生的事情。



为了踏实的享受 Ruby on Rails 带来的快感，首先，我们需要弄明白它试图去解决的问题。让我们来想象一个不存在 Rails 的世界。这很难，我确信。



如何只使用标准的 Ruby 类库来构建 Web 应用？在本文中，我将葱头介绍构建 Web 应用程序的主要基本概念。如果我们只能使用 Ruby 库构建 Web 应用程序，为什么我们需要 Web 服务器接口和 Ruby on Rails 之类的 Web 应用程序框架？在文本最后，你将对 Rails 背后的魔法，有新的认知。

