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



这意味着，若在共享模板的路由之间导航，则会重新创建DOM元素，并挂载组件的新实例。



您可以像创建普通布局一样创建模板。



![code example of template in nextjs13](https://res.cloudinary.com/practicaldev/image/fetch/s--3TfMD2cc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/agxc4hdrzyeh4gntg79y.png)



请注意，若并没有使用模板的具体原因，则应该使用布局。模板可能对以下情况有用：
