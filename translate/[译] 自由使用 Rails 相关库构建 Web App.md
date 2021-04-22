翻译自：https://shopify.engineering/building-web-app-ruby-rails



Ruby on Rails  是一个包含许多类库的 Web 框架，依赖这些类库，你可以创建以及分发 Web 应用。通常，我们使用 `rails new` 命令，以创建功能完整、包含大量特性的的 Web 应用程序。对我们大部分人来说，这已经足够了，Rails 的目标是使用魔法使用一切正常运行，而无需知道背后发生的事情。



为了踏实的享受 Ruby on Rails 带来的快感，首先，我们需要弄明白它试图去解决的问题。让我们来想象一个不存在 Rails 的世界。这很难，我确信。



如何只使用标准的 Ruby 类库来构建 Web 应用？在本文中，我将葱头介绍构建 Web 应用程序的主要基本概念。如果我们只能使用 Ruby 库构建 Web 应用程序，为什么我们需要 Web 服务器接口和 Ruby on Rails 之类的 Web 应用程序框架？在文本最后，你将对 Rails 背后的魔法，有新的认知。



### 本文介绍



我将介绍的主题包括：网络协议（TCP 和 HTTP），持久化数据存储，Web 服务器接口（Rack），以及 Rails 相关库（Action Controller，Action Dispatch，Active Record，和 Action View）。本文分为两部分，在本文第一部分，我们只使用 Ruby 核心库，这使得我们可以学习到构建 Web 应用并使其运行的关键概念。在第二部分，我们将使用 Rails 中的模块，替代之前的代码。



### 开始之前



我假设亲爱的读者你，知道如何使用文本编辑器，并浏览终端。您还熟悉 Internet 相关的术语，例如 Web 浏览器。你不一定要熟悉 Ruby 或者 Ruby on Rails 的相关数据结构，但如果熟悉，会很有帮助！最后，假设你正在使用 macOS，以及知道如何安装和使用 Ruby gem。



在任何时候，我都推荐你注意代码实例，并在电脑上亲自执行一边，这样可以让你获得完整的学习经验。不亲自上手，往往无法学到经验。



### The Mirth Web 应用程序



![ An image of the Mirth web application. The top of the screen displays a bulleted list with that displays 3 different birthdays.  Below the birthday information are two text boxes that allow the user to enter their name and Birthday. Below those fields is a button called Submit birthday.](https://cdn.shopify.com/s/files/1/0779/4361/files/How_to_Build_a_Web_App_with_and_without_Rails_Librariesimage14.png?format=webp&v=1616683871)

我们今天要构建的 Web 应用叫做 Mirth，他的主要功能是接受用户输入的日期，保存后，显示给用户。数据如何展示，由你决定。本教程的 Mirth，用于记录生日。你可以使用它记录周围人的生日，而不是依靠特定社交网站，来告诉我们别人的生日。



