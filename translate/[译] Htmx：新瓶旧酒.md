翻译自：[Htmx: The newest old way to make web apps - LogRocket Blog](https://blog.logrocket.com/htmx-the-newest-old-way-to-make-web-apps/)



### Htmx：新瓶旧酒



#### 介绍



Htmx 是一个 JavaScript 库，可以有效的处理 AJAX 请求，触发 CSS 动画，以及调用 WebSocket 和 服务事件。Htmx 让你通过简单的标记，来构建现代的，给力的用户交互。



这个库的体积约10KB（min.gz，它是无依赖的（也就是说，无需依赖其他 JavaScript 包来运行），而且它也兼容IE11。



在本教程中，我们将探索htmx的强大功能，同时涵盖以下部分：

- 安装htmx
- 用htmx发送AJAX请求自定义htmx
- 输入验证用htmx触发CSS动画
- 通过 htmx 来触发 CSS



#### 安装 htmx

你可以通过下载htmx源文件或者直接在你的标记中包含它的CDN来开始使用htmx，如下所示

```
<script src="https://unpkg.com/htmx.org@1.3.3"></script>
```

上面的脚本，将加载当前的 htmx 稳定版，到你的网页，本文写作时为 1.3.3。加载完毕后，你就可以在你的网页上，使用 htmx 了。



#### 通过 htmx 发送 AJAX



Htmx 提供一组属性，来实现直接通过 HTML 元素，来发送 AJAX 请求的功能。这些属性包括：



- `hx-get` — 发送 `GET` 请求，到指定的URL
- `hx-post` — 发送 `POST` 请求，到指定的URL
- `hx-put` — 发送 `PUT` 请求，到指定的URL
- `hx-patch` — 发送 `PATCH` 请求，到指定的URL
- `hx-delete` — 发送 `DELETE` 请求，到指定的URL



#### 代码示例

```
<button hx-get="http://localhost/todos">Load Todos</button>
```







