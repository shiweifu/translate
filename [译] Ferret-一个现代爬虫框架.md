原文地址：
[https://medium.com/@ziflex/say-hello-to-ferret-a-modern-web-scraping-tool-5c9cc85ba183](https://medium.com/@ziflex/say-hello-to-ferret-a-modern-web-scraping-tool-5c9cc85ba183)


作为开发者，你可能搞过好多 Web 爬虫去爬取数据从网络上。这些数据被用在 AI 项目、旅行 App 的酒店预定比价等等很多需要数据的情况。

当数据量小的时候，写爬虫是不是什么难事。但是当数据量大的时候，或者对频率有要求的时候，就完全不是那么回事儿了。

这是我遇到的问题。一段时间以前，我开启了一个重数据驱动的项目。这个项目需要美容品牌和产品信息，产品的成分和用在不同人皮肤的效果。数据量不算特别恐怖，但是已经足够大。

我的第一反映，就是利用一些现成的工具去完成这项任务。我也确实把活儿干完了。又过了一端时间，新的问题来了，接下来，我要讲讲我到底遇到了什么。


### 问题

构建一个稳定、可伸缩的爬虫系统，有许多问题需要解决。好消息是，许多问题选择合适的库或框架都可以解决。但是有些重要的问题我无法解决：

- 为了使库或框架很好的工作，你必须写很多胶水代码。这些代码是相似且无聊的
- 要爬取的页面结构常常变更，可能需要频繁的跟进这些更新，以及重新部署。
- 时至今日，越来越多的网站使用动态页面，简单的 HTTP GET 请求无法很好的工作。更糟糕的是有的页面还需要用户交互之后才能得到数据

当然，这些问题都是可解决的，但每次都解决相似的问题，真的好吗？

于是我开始寻找现成的工具解决我遇到的这些问题，不幸的是，没有找到合适的解决方案。于是我决定自己构建一个。

### 方案

![image.png](https://upload-images.jianshu.io/upload_images/3115-e68785875a506cbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Ferret 被创造出来，一个现代 Web 爬取工具，通过声明式查询语言进行工作。其内置DSL功能强大而又具备可扩展性。

与编写胶水代码相比，它的抽象级别很高，让使用者可以专注在处理数据上。

Ferret 既提供 CLI，也可以很方便的以嵌入的方式执行。

良好的扩展性当然也必不可少。你不仅可以扩展函数，还可以自定义数据类型。运行时都提供支持。

它的构建思想很简单，使用者想清楚想要得到什么，使用内置的查询语言描述出来，余下的工作交给 Ferret。


### 代码时刻

让我们看看它是如何工作的。

举个例子，如果要爬取 GitHub 的趋势列表，使用以下代码：

```
LET page = DOCUMENT("https://github.com/trending")
FOR row IN ELEMENTS(page, "ol.repo-list li")
    LET name = INNER_TEXT(row, "div:nth-child(1)")
    LET description = INNER_TEXT(row, "div:nth-child(3)")
    RETURN { name, description }
```

> 提示：FQL（Ferret Query Language）从 AQL（ArangoDB Query Language）的语法上借鉴很多。如果这些代码让你觉得眼熟，那你的感觉是对的。

上面的代码只有寥寥数行，但却解决了我们刚刚提出来的三个问题中的两个：构建重复的代码和频繁的网页标记修改。稍后我们将讨论剩下的那个问题。

让我们近距离看一下。

第一个问题在 Ferret 引入了内置语言后，不再是问题 --- Ferret 让用户远离那些胶水代码。用户只需要关心他们想要爬取什么数据，以及什么样的数据需要被输出。内置的声明式语言让这些内容十分可读。

此外，FQL 是解释执行的，这意味着第二个问题也被解决。脚本更新无需重新部署。你可以简单的对你的爬虫进行修改。

现在，让我们讨论一下上面那段的脚本在 Ferret 中是如何被执行的。

首先，在 Ferret 中加载页面内容，通过调用内置的 `DOCUMENT` 方法。Ferret 在内存中创建对象。然后我们为这个对象分配一个变量。FQL 支持定义变量，这是一个十分有用的特性。

在页面被加载进内存后，我们使用 `ELEMENTS` 方法寻找全部我们关心的页面内容并生成一个列表，然后使用 FQL 内置的 FOR IN 关键字进行遍历，许多开发者会对这个构建流程感到熟悉。查找页面内使用的是标准的 CSS 选择器语法，就像我们在浏览器开发者工具中使用的一样。

FOR 循环内部，我们通过内置的 INNER_TEXT 函数得到了元素的文本。这里为了简化和更有效率，我们又一次使用变量和 CSS 选择器。

得到所有我们感兴趣的元素中的文本后，我们使用 `RETURN` 关键字，后面跟着一个对象。对象的语法和 JavaScript 中的对象很类似，主要是属性名和值。每个返回的对象都包含两个属性：name 和 description。RETURN 关键字做的工作像一个聚合器，最终我们得到的是一个满是对象的数组。

至此，我们可以简单又便捷的爬取网上的数据。

### 动态页面

上面的代码只解决了两个问题，我们仍然不能爬取动态页面。

如果我们想要爬取 SongCloud，我们该怎么做？如果我们执行下面的代码，我们将得到一个空数组：

```
LET doc = DOCUMENT('https://soundcloud.com/charts/top) 
LET tracks = ELEMENTS(doc, '.chartTrack__details')
FOR track IN tracks
    RETURN {
        artist: TRIM(INNER_TEXT(track, '.chartTrack__username')),
        track: TRIM(INNER_TEXT(track, '.chartTrack__title'))
    }
```

要解决这个问题，我们需要做两件事：

- 运行 Chrome 或 Chromeium 在 Docker 中，或者带上 `--remote-debugging-port=9222` 参数。
- 带上 Boolean 类型参数给 DOCUMENT 函数，这将告诉 Ferret 页面是动态加载的，它会选用不同的驱动。

更新后的代码：

```
LET doc = DOCUMENT('https://soundcloud.com/charts/top', true)
WAIT_ELEMENT(doc, '.chartTrack__details', 5000)
LET tracks = ELEMENTS(doc, '.chartTrack__details')
FOR track IN tracks
    RETURN {
        artist: TRIM(INNER_TEXT(track, '.chartTrack__username')),
        track: TRIM(INNER_TEXT(track, '.chartTrack__title'))
```

你也许注意到了新增加的一行代码：`WAIT_ELEMENT(doc, ‘.chartTrack__details’, 5000)`。当使用动态模式时，我们需要确保想要数据已经渲染好。所以我们阻塞执行过程，直到想要的数据出现，或者超时。

当需要的数据已经加载完毕，我们就可以遍历和解析他们了。

如你所见，我们所的查询语句并没有什么变化。这时我们已经可以拿到正确渲染后的数据，接下来我们需要告诉 Ferret 我们打算如何处理他们。

### 页面交互

上面的例子也许不够给力，无法给你足够的理由在真实的复杂环境中使用 Ferret。

在爬取数据的时候，我们需要和网页有一些交互，就像真实用户那样。点击，输入数据，或者鼠标移动 -- 这些特性都需要被支持。

恰好这些特性 Ferret 都支持。

假设我们要构建一个网站，聚合商品的价格从流行的购物网站上获取信息，比如Amazon和eBay。

为了获取商品的列表及信息，我们的实现步骤：

- 输入搜索内容到输入框
- 点击搜索按钮
- 等待页面返回数据并渲染好
- 遍历数据
- 移动到下一页
- 回到步骤三，再来一次

让我们拿 Amazon.com 尝试一下：[https://github.com/MontFerret/ferret/blob/master/examples/pagination.fql](https://github.com/MontFerret/ferret/blob/master/examples/pagination.fql)

提示：不幸的是 Medium 对字体限制很严格，所以查询的内容可读性受到一些影响。

到现在为止，FQL 这门语言的大多数语法大家应该比较熟悉了，接下来我讲一下之前没提到的内容。

首先，[数据输入](https://github.com/MontFerret/ferret/blob/master/examples/pagination.fql#L3) 和提交表单通过(点击搜索按钮)[https://github.com/MontFerret/ferret/blob/master/examples/pagination.fql#L4]。需要注意的是，我们提交了参数（以@符号开头）在这个脚本。这个参数值可以通过 CLI 获取，也可以使用嵌入模式（下一篇博客中，我讲到如何使用嵌入模式）。

(在结果返回后)[https://github.com/MontFerret/ferret/blob/master/examples/pagination.fql#L5]，我们需要向前翻页。理想情况下，你需要根据计算出接下来的页码并使用其作为参数，但这里我们简化这个步骤。

每个遍历，我们需要访问下一页（除了第一页）(通过点击翻页按钮)[https://github.com/MontFerret/ferret/blob/master/examples/pagination.fql#L18]，然后等待加载完成。接着就是解析内容。

如你所见，大多数行为类似一个普通用户所做的。这是个很好的使用`FQL`及Ferret 爬取网页内容的的示范。

(这里)[https://github.com/MontFerret/ferret/tree/master/examples]可以找到更多的例子和使用场景，也欢迎提PR。

### 谁需要它？

非常合理又常见的问题。

好吧，我就是目标用户。

对于其他人，我建议数据分析员，ML/AI 研究者，QA工作者和其他任何想爬取网站数据，但又不想构建新的Python/Node项目来爬取的用户。

### 下一步

这个项目一直在成长，一个里程碑是稳定的 API 和提供开箱即用丰富的功能。除了 Ferret 本身，其他周边项目也在计划构建中：

#### Ferret Server

也许你已经注意到了，Ferret 只是一个库加上友好的命令行接口。它还没有提供产品化的解决方案应对复杂的情况。这就是 Ferret Server 要解决的目标 --- 提供一个 Web 爬取平台。这个平台允许你存储脚本和存储爬取的数据到数据库，手动执行脚本或者按计划调度，通知第三方系统等等功能。这个项目已经启动，但是还处在非常早期的状态。

#### 更多的 HTML 驱动

拥有更多的 HTML 驱动，将会让 QA 与开发者更容易测试他们的 Web Apps 在不同的浏览器使用相同的脚本。

#### Ferret Registry

就像是 FQL 脚本版本的NPM registry，在这里用户可以分享自己的和浏览别人上传的脚本让爬取更加简单。

#### Jupyter kernel

我不是很了解 Jupyter，但是我知道许多数据分析师使用它，所以我觉得如果集成 Ferret 到 Jupyter，会使 Ferret 更有用。

最后，如果你有许多空闲时间，想要贡献到开源项目，十分欢迎。

 
