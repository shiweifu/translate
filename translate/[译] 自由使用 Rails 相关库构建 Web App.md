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



### 讨论网络



现在我们已经梳理出要做的事情了。接下来，让我们以构建一个简单的 Web 应用程序，并使其工作作为开始。一个 Web 应用或者一个服务器如何在 Internet 上收发信息的呢？与我们人类相似，网络应用程序之间，使用特定的协议进行通信，这个协议叫做 [Transmission Control Protocol](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) (TCP)。你也许已经听过 TCP 的名字，但 TCP 究竟在网络应用程序中，起到什么作用呢？



要弄清楚 TCP 究竟做了什么，我们首先需要了解  [Internet Protocol](https://en.wikipedia.org/wiki/Internet_Protocol) (IP) 协议。稍后你会发现，抽象等级，决定了他们的工作方式。TCP 只是 IP 的抽象，IP 是一种较低级别的协议，用于传送数据包。



虽然 IP 使互联网可以运行起来的基础，但因为它过于底层，因此如果直接使用它传输，并不可靠。直接使用 IP 传输，有可能会造成错误的数据包或者根本无法传送。TCP 建立在 IP 之上，通过增加了一些特性，来使传输更加可靠。为了解决数据包发送顺序错乱的问题，TCP 允许发送方在数据包内添加一个自动递增的编号，该编号在数据接收方所收到的数据末端重新组合。TCP 的另一个关键特性是引入了套接字的概念。



### 创建套接字



套接字，用于在服务端和客户端之间建立持久连接。Ruby 的核心库包括套接字 [sockets](https://ruby-doc.org/stdlib-2.7.0/libdoc/socket/rdoc/Socket.html) ，它的实现与你的操作系统有关（顺便说一下，这也是一种抽象）。

