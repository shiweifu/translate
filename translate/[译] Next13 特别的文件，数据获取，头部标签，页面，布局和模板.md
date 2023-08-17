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
