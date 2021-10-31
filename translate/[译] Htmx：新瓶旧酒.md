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



上面的代码示例告诉浏览器当用户单击该按钮时，它将Get请求（HX-Get）发送到提供的URL，在这种情况下是 http://localhost/todos。



![Htmx get-request](https://blog.logrocket.com/wp-content/uploads/2021/05/htmx-get-request.gif)

默认情况下，从任何htmx请求返回的响应将被加载到发送请求的当前元素中。在AJAX请求的目标元素一节中，我们将探讨如何在另一个HTML元素中加载响应。



针对AJAX请求的元素一节，我们将探索如何在另一个HTML元素中加载响应。



#### 触发请求



应该注意，htmx中的AJAX请求是由元素的自然事件触发的。例如，输入、选择和textarea是由onchange事件触发的，表单是由onsubmit事件触发的，其他所有事情都是由onclick事件触发的。



当你想要修改触发请求的事件时，htmx提供了一个特殊的hx-trigger属性



```
<div hx-get="http://localhost/todos" hx-trigger="mouseenter">
    Mouse over me!
</div>
```



在上面的示例中，当且仅当用户的鼠标悬停在div上时，GET请求将被发送到提供的URL。



#### 触发修饰符



上一节提到的hx-trigger属性接受一个额外的修饰符来更改触发器的行为。可用的触发器修饰符包括



- `once`：保证请求只发生一次
- `changed`：如果HTML元素的值发生变化，则发出请求
- `delay:<time interval>`：延迟:<时间间隔>在发出请求之前等待给定的时间(例如，Delay -1)。如果事件再次触发，则复位倒计时
- throttle:<time interval>：在发送请求之前等待给定的时间(例如，throttle:1s)。但与延迟不同的是，如果在达到时间限制之前发生新事件，则该事件将在队列中，以便在前一个事件结束时触发
- `from:<css selector>`：在不同元素上，监听事件



#### 代码示例



```
<input
    type="text"
    hx-get="http://localhost/search"
    hx-trigger="keyup changed delay:500ms" />
```



上面的代码例子中，用户触发了一次 `keyup` 事件，在输入组件上（比如用户在输入框输入任意文本）及其先前的值更改，浏览器将自动将 GET 请求 ，发送到 `http://localhost/search`，中间延迟 500 毫秒。



### 通过 `htmx-trigger` 进行轮询



在 `htmx-trigger` 属性中，你可以指定每间隔 n 秒，触发一次事件，而不是等待触发请求。通过这个选项，你可以每隔 n 秒像特定的 URL 发送一个请求。



```
  <div hx-get="/history" hx-trigger="every 2s">
  </div>
```



上面的代码示例，告诉浏览器每隔 2 秒，向 `/history` 发送一个请求，并将返回结果加载到对应 div 中。



#### 针对某个元素的 AJAX 请求



在前面的小节，我们提到过 htmx 中，AJAX 请求的响应，将加载到触发请求发起的元素中。如果需要将请求的结果加载到不同的元素中，可以使用 `hx-target` 属性来实现这一点。这个属性接受一个 `CSS 选择器`，并使用指定的选择器，自动的将 AJAX 响应，注入到 HTML 元素。



