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



我们可以修改我们的 todo 应用，来适应这种情况：



```
<button hx-get="http://localhost/todos" hx-target="#result">
    Load Todos
</button>
<div id="result"></div>
```



与前面的例子不同，这个新代码示例，向 `http://localhost/todos` 发送请求，然后将返回的内容加载到 `id=result` 的 `div` 中。



#### 使用返回的 HTML，替换页面上的 DOM



与 `hx-target` 类似，`hx-swap` 属性，定义了如何处理 AJAX 返回的内容。其所支持的值包括：



- `innerHTML` - 默认选项，加载 AJAX 返回的内容，到触发请求的元素内部的 HTML。
- `outerHTML` - 该选项将会使用返回内容，替换整个触发请求的元素。
- `afterbegin` - 将请求返回的内容，作为触发请求元素的第一个子元素。
- `beforebegin` - 将请求返回的内容，作为触发请求元素的父元素。
- `beforeend` - 将请求返回的内容，添加到触发请求的元素最后一个子元素前面。
- `afterend` - 与前面的属性不同，这个选项将请求返回的内容添加到触发元素的后面
- `none` - 这个选项什么也不做



#### 请求指示器



当发送 AJAX 请求时，让用户知道网络请求正在被调用，并展示一个指示器，是一个改善体验的实践。默认情况下，浏览器不会显示，你可以通过添加 `htmx-indicator` 类，来交给 `htmx` 处理。



下面是相关代码：

```
<div hx-get="http://path/to/api">
     <button>Click Me!</button>
     <img
        class="htmx-indicator"
        src="path/to/spinner.gif"
      />
</div>
```



默认情况下，`htmx-indicator` 这个指示器的透明度为 0，因此，它不可见，但存在于 DOM 中。



而且，当您发出AJAX请求时，htmx会自动向发送请求的元素添加一个新的htmx-request类。这个新的html -request类将导致带有html -indicator类的子元素的不透明度转换为1，从而显示该指示符。



![htmx-indicator Example](https://blog.logrocket.com/wp-content/uploads/2021/05/htmx-indicator-example.gif)

### 请求数据



如果你的 AJAX 请求，通过 form 或者输入组件触发，默认情况下，htmx 将会在请求中，自动引入所在触发组件的全部值。



但是如果你想要包含其他元素的值，你可以使用hx-include属性和CSS选择器来选择你想要包含在请求中的所有元素的值。



### 代码示例



```
<div>
    <button hx-post="http://path/to/api/register" hx-include="[name=username]">
        Register!
    </button>
    Enter Username: <input name="username" type="text"/>
</div>
```



就像在上面的代码示例中一样，当您向/寄存器端点发出请求时，您的AJAX请求将自动包含其正文中的电子邮件字段。



### 过滤输出参数



Htmx 同样提供另一个 `htmx-params` 属性，您可以使用该属性过滤发送AJAX请求时提交的唯一参数。

```
<div hx-get="http://path/to/api/example" hx-params="*">
    Send Request
</div>
```



上面的代码示例，将该页面中的所有输入组件都引入并作为你的请求参数。



该属性的取值包括：



- `*` - 引入当前页面中所有的输入组件，并作为 AJAX 的参数发送
- `none` - 不引入页面中任何内容作为请求参数
- `not <param-list>` - 引入所有非逗号分隔的参数列表
- `<param-list>` - 只引入逗号分隔的内容作为参数



### 上传文件



使用HTMX，您可以通过将HX编码属性添加到发送请求的实际元素的父元素，轻松地将诸如图像，视频，PDFS等的文件（如图像，视频，PDF）等文件发送到处理，以便进行处理：

```
<form hx-encoding="multipart/form-data">
    Select File:
    <input type="file" name="myFile" />
    <button
      hx-post="http://path/to/api/register"
      hx-include="[name='myFile']"
    >
      Upload File!
    </button>
</form>
```



### 自定义 htmx 输入验证



默认情况下，Htmx 与 HTML 5 的验证 API 集成，如果验证失败，则请求不会被发送。此功能适用于 AJAX 请求和 WebSocket 事件。



除此之外，htmx还会触发与验证相关的事件，这在自定义输入验证和错误处理中非常有用。



当前可用的验证事件包括：



- `htmx:validation:validate` - 此事件在添加自定义验证登录时非常有用，因为它在验证元素之前被调用。
- `htmx:validation:failed` - 当元素验证返回false时，即表示输入无效时，将触发此事件
- `htmx:validation:halted` - 当元素由于输入验证错误而无法发出请求时，将调用此事件



### 通过 htmx 触发 CSS 动画









