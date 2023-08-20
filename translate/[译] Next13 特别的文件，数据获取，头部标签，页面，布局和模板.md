翻译自：[Next.js 13 special files, data fetching and head tag — page, layout and template - DEV Community](https://dev.to/oskidev/nextjs-13-special-files-data-fetching-and-head-tag-page-layout-and-template-1hh)

在开始之前，请注意 Next.js 13 中的默认组件是服务器组件。这大致意味着你不能使用客户端的效果和操作，如`、useState、onClick 等。你可以通过添加“use client”来使组件成为客户端；该指令位于导入组件之上。

服务器组件还通过减少发送到客户端的 JavaScript 数量来提高性能，使其更容易获取数据，更适合 SEO，因为搜索引擎机器人可以轻松访问页面的 HTML 内容，并且不需要等待客户端代码执行。

如果你想了解路由和应用程序目录的基本知识，你可以访问[上一篇文章](https://medium.com/@gkarol/next-js-13-app-directory-routing-994cf0f9b52c)。

## 页面

新应用程序目录中最重要的文件是页面。这是因为页面文件使路由可以公开访问。

您可以通过从 page.tsx 文件中导出函数作为默认值来创建页面，如下例所示。您可以随意调用该函数。

![Code example of home page in nextjs 13](https://res.cloudinary.com/practicaldev/image/fetch/s--0AlfNZrz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/71qre9xl3bl3dc3nel60.png)

## 布局

在 layout.tsx 中，您可以创建一个在多个子页面之间共享的 UI。因此，您可以为/company/about 创建一个用户界面，该布局将应用于 route/company/about/\*下的所有页面，但不适用于/company/product 等同级路由。



你可以在任何层级的路由中，创建需要的布局文件。



在导航时，布局会保留状态，保持交互式，并且不会重新渲染。



您可以通过将函数作为默认值从layout.tsx文件导出来创建布局。



请注意，最上面的布局函数称为RootLayout，任何其他布局都可以根据需要调用。在下面的例子中，你可以看到RootLayout，这是必需的。



![code example of root layout in nextjs 13](https://res.cloudinary.com/practicaldev/image/fetch/s--76_aW9bn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/azcywj74hkm651qpxjcc.png)



在根布局中，应该包含html和body标记。



任何其他布局都可以是这样的（页面的布局/关于页面的布局）



![code example of common layout in nextjs13](https://res.cloudinary.com/practicaldev/image/fetch/s--r2HxCljO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s0idx5xclimjcl6ctt67.png)



## 模板



模板与布局相似，它们也包装每个子布局和页面，但模板在导航时为每个子布局创建一个新实例，而不是保留在路线上并保持状态的布局。

~~~~~~~~

这意味着，若在共享模板的路由之间导航，则会重新创建DOM元素，并挂载组件的新实例。



您可以像创建普通布局一样创建模板。



![code example of template in nextjs13](https://res.cloudinary.com/practicaldev/image/fetch/s--3TfMD2cc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/agxc4hdrzyeh4gntg79y.png)



请注意，若并没有使用模板的具体原因，则应该使用布局。模板可能对以下情况有用：



- 使用CSS或动画库输入/退出动画。

- 依赖useEffect（例如记录页面视图）和useState（例如每页反馈表单）的功能。

- 更改默认框架行为。例如，布局内部的悬念边界仅显示后备时第一次加载布局，而不是切换页面时。对于模板，每个导航都显示了后备。



### 数据获取



页面和布局与其他组件一样可以获取数据。由于有了新的服务器组件，在Next.js 13中获取数据变得非常方便，而且有 [许多优点](https://beta.nextjs.org/docs/data-fetching/fundamentals#fetching-data-on-the-server)。



您还可以在客户端组件中获取数据。建议在客户端组件中使用 [SWR](https://swr.vercel.app/) 或者 [React Query](https://tanstack.com/query/v4/)等库。（将来也可以使用use() 钩子）。



您可以使用本机fetch（）Web API访问新的数据获取系统，并在服务器组件中使用async/await。



您可以在多个组件中获取数据，Next.js 13将自动在临时缓存中缓存具有相同输入的请求（GET）。这样可以避免多次提取相同数据的情况。它对于在布局中获取数据很有用，因为它不可能在父布局及其子布局之间传递数据。只需直接在需要的布局中获取数据，Next.js 13将负责缓存和消除请求。



### 两种数据获取模式

- *并行数据获取* - 如果没有一个依赖于前一个查询的查询，则可以使用并行数据获取。这减少了客户端-服务器瀑布和加载数据所需的总时间，因为路由中的请求会被急切地启动并同时开始获取。

- *顺序数据获取* - 如果有一个查询依赖于前一个查询，则可以使用顺序数据获取。这可能会导致更长的加载时间，但在一次获取取决于前一次获取的结果的情况下，这很有用。



如果您想了解更多关于数据获取模式的信息，请 [访问 Next.js 13 文档](https://beta.nextjs.org/docs/data-fetching/fetching#data-fetching-patterns)。

如果你想在Next.js 13中扩展你对数据获取的一般知识，[这里有链接](https://beta.nextjs.org/docs/data-fetching/fundamentals)。



## 修改头部标签



还有一件事你应该知道的是一种修改头标签的新方法。



如果在head标记中使用静态值：



![Static way to modifying head tag in nextjs13](https://res.cloudinary.com/practicaldev/image/fetch/s--lsMJ7Zd6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yb6waf9x2tytbovhffya.png)



如果你想使用动态头部标签：



![Dynamin way to modifying head tag in nextjs13](https://res.cloudinary.com/practicaldev/image/fetch/s--R8kRb0pe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uro5ybahuclby3yhyc42.png)



这就是本文的全部内容。如果你想扩展你的知识，请[查看文档](https://beta.nextjs.org/docs/getting-started)。

我希望你喜欢！🚀
