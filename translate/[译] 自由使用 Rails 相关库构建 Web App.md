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



#### 使用 Ruby 的 Socket 库



```
#Use Rubys Socket Library
require 'socket'

server = TCPServer.new(1337)
```



Socket 库允许你创建一个 TCP 服务端套接字。在我们的例子中，我们想要 Mirth 的服务端套接字接收访问的连接。那么，我们如何知道有外部的连接访问我们的服务端呢？TCP 创建了一个套接字，处理应用指定的端口。在本例中，我们的 Mirth 所使用的端口是 1337。



#### 接收输入链接



```
#Accept Incoming Connections
loop do
  client = server.accept
```



现在，我们的服务端套接字已经创建，当客户端连接的时候，我们决定如何处理。在本例中，我们通过 `#accept` 方法来接收任何外部连接请求，然后使用 IO 对象来读取信息。



### 关闭客户端 Socket



```
#Close the Client Socket 

  puts "Closing client socket"
  client.puts "Goodbye #{input}"
  client.close
```



当我们处理完对客户端连接的响应，我们需要关闭连接。



### 运行代码创建服务端 Socket 和 客户端 Socket



在此可以看到完整的代码： [`mirth-1.rb` on Github](https://gist.github.com/ShopifyEng/380218296a07109ebdb359fcafa06d62#file-mirth-1-rb).



1. 下载了代码后，要在你的机器上运行代码，执行 `ruby mirth-1.rb`。此时你创建了一个服务端套接字。现在你需要一个客户端来连接这个服务端。
2. 打开终端，执行 `nc localhost 1337`。这样就通过 [netcat](https://en.wikipedia.org/wiki/Netcat) 连接上了我们本地的服务端，1337 是服务端监听的端口号。在建立连接后的任何输入，将会被输出。



### 更加结构化的访问网络



现在，我们已经可以创建 TCP 套接字了，是时候向更高级别的抽象前进了。当前我们的 TCP 服务端还不能与浏览器通信。因为浏览器是使用的是超文本传输协议（HTTP）。TCP 是应用层的协议，所以任何基于应用层的应用都可以使用 TCP。而 HTTP，只是用在 Web 领域。所以我们可以使用 Ruby，来实现 HTTP。



![A directed network diagram showing the layers of abstraction between 3 network protocols (IP, TCP, HTTP). The bottom layer is the Network Layer that uses IP. That flows up to the Transport Layer that uses TCP. That then flows to the Application Layer which using HTTP.](https://cdn.shopify.com/s/files/1/0779/4361/files/How_to_Build_a_Web_App_with_and_without_Rails_Librariesimage18.png?format=webp&v=1616683871)

世界上有许多不同类型的浏览器和服务器，而浏览器都支持 HTTP 协议，这就是为什么我们需要支持 HTTP 协议的原因。该协议是由 HTTP 工作组建立的。



每个 HTTP 请求，都由相同的结构组成：



- 开始行
- 空或多个请求头
- 空白行
- 信息内容



典型的 HTTP，由两部分组成：请求和响应。Web 浏览器向服务端发送请求，以获取页面，服务端对这个请求进行响应。HTTP 请求和响应的区别只是 HTTP 消息的 `start-line` 不同，请求使用 `request-line`，响应使用 `status-line`。有关描述 HTTP 请求和响应的细节的文档，被称为 RFC。



让我们先暂停，看一下请求和响应。



### 构建请求



从 Mirth Web 应用的角度，在连接建立之后，它需要响应请求。与前面的例子不同，我们此时需要发送一个真正的 HTTP 请求，而不是字符串。



**HTTP请求的关键组件是请求行，其中包含供Web应用程序执行操作的重要信息。 这些是方法令牌，目标路径和HTTP协议版本号。 方法令牌是来自服务器的请求的类型，两个常见的请求是GET（获取数据）和POST（发布数据以在服务器中进行更改）。 如您所料，方法令牌根据请求内容将代码引导至不同的路径。 目标是请求来自的路径。 例如，服务器从用户请求信息到该网页www.mirth.com/baked-brownies/123的目标路径是baked-brownies / 12。 最后，版本号是Web浏览器使用的HTTP版本，最常见的是HTTP / 1.1。**



### 获取请求的 Request-line 



```
#Get the Request-line of the Request
  request_line = client.readline

  puts "The HTTP request line looks like this:"
  puts request_line
```



**服务器接受客户端套接字后，我们将读取请求的请求行。 与我们之前使用的#gets方法不同，如果没有更多输入要获取，＃readline将返回错误。



### 分割请求的 Rquest-line 



```

# Breaks down the HTTP request from the client
  method_token, target, version_number = request_line.split 
  response_body =  "✅ Received a #{method_token} request to #{target} with #{version_number}"

  client.puts response_body
  client.close
end
```



我们将HTTP请求细分为method_token，target和version_number。 所有这些部分对于服务器如何响应请求都是必不可少的信息。 该代码仅打印从HTTP请求中获取的信息，并将其返回到HTTP请求的正文中以显示在Web浏览器页面上。