翻译自：[My current HTML boilerplate - Manuel Matuzović](https://www.matuzo.at/blog/html-boilerplate/)



下面是我的 HTML 中所用到的元素，我将逐个解释它们。



通常，当新项目开始时，我会完整的复制一个 HTML 的结构。这个 HTML 结构要么来自于我最近构建的网站，要么直接从 [HTML5 Boilerplate](https://html5boilerplate.com/) 项目复制。最近我没有开启新的项目，但我想要记录一下构建站点时所使用的结构。因此，只是复制粘贴是不够的，我必须了解这些结构的背后含意。于是我花了许多时间研究和整理这些结构，这也是这篇文章打算分享给您的内容。



> Cool, this is like https://github.com/h5bp/html5-boilerplate but a little worse.
>
> -Random reply guy on Hacker News



### 我的 bolierplate



这是一份完整的文件。下拉有更多细节。



```html
<!DOCTYPE html>
<html lang="en" class="no-js">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width">

  <title>Unique page title - My Site</title>

  <script type="module">
    document.documentElement.classList.remove('no-js');
    document.documentElement.classList.add('js');
  </script>

  <link rel="stylesheet" href="/assets/css/styles.css">
  <link rel="stylesheet" href="/assets/css/print.css" media="print">

  <meta name="description" content="Page description">
  <meta property="og:title" content="Unique page title - My Site">
  <meta property="og:description" content="Page description">
  <meta property="og:image" content="https://www.mywebsite.com/image.jpg">
  <meta property="og:image:alt" content="Image description">
  <meta property="og:locale" content="en_GB">
  <meta property="og:type" content="website">
  <meta name="twitter:card" content="summary_large_image">
  <meta property="og:url" content="https://www.mywebsite.com/page">
  <link rel="canonical" href="https://www.mywebsite.com/page">

  <link rel="icon" href="/favicon.ico">
  <link rel="icon" href="/favicon.svg" type="image/svg+xml">
  <link rel="apple-touch-icon" href="/apple-touch-icon.png">
  <link rel="manifest" href="/my.webmanifest">
  <meta name="theme-color" content="#FF00FF">
</head>

<body>
  <!-- Content -->
  <script src="/assets/js/xy-polyfill.js" nomodule></script>
  <script src="/assets/js/script.js" type="module"></script>
</body>
</html>
```



下面我们来助航介绍。



### 通用部分



```
<!DOCTYPE html>
```



对于老派的你来说，你无需学习任何其他 doc 类型。因为这是唯一的 doc 类型。时至今日，也没有其他的 doctype。显式声明是为了[兼容性的原因](https://developer.mozilla.org/en-US/docs/Web/HTML/Quirks_Mode_and_Standards_Mode)。



```
<html lang="en" class="no-js">
```



`lang` 属性，是 HTML 结构中最重要的属性，因为他用处广泛，可以对许多地方产生影响。你可以阅读有关它的更多信息：[On Use of the Lang Attribute](https://adrianroselli.com/2015/01/on-use-of-lang-attribute.html) 和 [The lang attribute: browsers telling lies, telling sweet little lies](https://www.matuzo.at/blog/lang-attribute/)。对于 `html` 结构而言， 它声明了当前页面所使用的字眼语言。它包含一个`语言标签`，格式定义在此：[Tags for Identifying Languages (BCP47)](https://www.ietf.org/rfc/bcp/bcp47.txt)，锯割例子， `en` 对应 英语， `de` 对应德语，以及 `ru` 对应俄语。



- [lang Language tag syntax MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/lang)

在此方案中，当浏览器不支持 JavaScript 时，我在这个组件上增加 `no-js`。在支持和可以执行 `JavaScript` 的现代浏览器中，则将此类删除。



```
<meta charset="UTF-8">
```



这个属性声明了文档所使用的字符编码。如果没有它，则可能导致字符在某些浏览器中无法正确显示。



以下是 Safari 显示我的名字方式，分别为带 `charset` tag 和不带。



```
<meta name="viewport" content="width=device-width, initial-scale=1">
```



视口标签允许我们改变视口的宽度，在响应式 Web 设计中，这是必须的。`width=device-width` 指令设置视口的宽度与浏览器的宽度相同。`initial-scale` 可以当浏览器第一次加载页面的时候，控制缩放。



我不确定是否仍需要设置initial-scale = 1。 我相信我在某个地方读到，只有<iOS 9上的Safari才需要它，但是我找不到该文章了。



视口元标记应尽可能早地出现在文档中，以确保正确呈现文档。



自iOS 9.3起，我们不再需要执行rinkle-to-fit = no选项。

- [Time to remove the shrink-to-fit=no band aid?](https://www.scottohara.me/blog/2018/12/11/shrink-to-fit.html)

- [Using the viewport meta tag to control layout on mobile browsers](https://developer.mozilla.org/en-US/docs/Web/HTML/Viewport_meta_tag)



### 标题，描述，以及社交媒体



```
<title>Unique page title - My Site</title>
```



页面的唯一标题。 它显示在很多地方，例如，在浏览器标签上，在搜索引擎结果中，将页面另存为书签时等。



- [Provide informative, unique page titles](https://www.w3.org/WAI/tips/writing/#provide-informative-unique-page-titles)
- [Accessible page titles in a Single Page App](https://hiddedevries.nl/en/blog/2018-07-19-accessible-page-titles-in-a-single-page-app)



```
<script type="module">
  document.documentElement.classList.remove('no-js');
  document.documentElement.classList.add('js');
</script>
```



我在 JS 模块支持。如果浏览器支持 JavaScript 模块，意味着浏览器支持现代 JavaScript 特性，如 模块，ES 6 语法，fetch 等等。大多数 JS 仅提供给这些浏览器，此外，当 JS 是被激活的，而组件的样式不同，这种情况下我使用 `js` 类。



![caniuse showing that all modern browser support JS modules](https://www.matuzo.at/images/caniuse_jsmodules750.png)



```
<link rel="stylesheet" href="/assets/css/styles.css">
```

当前网站的样式表。



```
<link rel="stylesheet" href="/assets/css/print.css" media="print">
```

- [我常常忘记给打印设备的样式表](https://www.matuzo.at/blog/i-totally-forgot-about-print-style-sheets/)



```
<meta name="description" content="Page description">
```



针对页面内容的描述，用于诸如显示浏览器页面搜索结果的场景下。可以是任何长度，但搜索引擎会将其裁剪到 ~155-160 字符。



```
<meta property="og:title" content="Unique page title - My Site">
```



页面的唯一标题。用于社交平台的 URL 爬虫，如 Twitter、Facebook 等。



```html
<meta property="og:image" content="image.jpg">
```



当你将网页分享到社交网络、聊天工具或者其他网站时，这个图片会被显示。



理想情况下，这个图片是一个正方形的图片，重要内容以二比一的形式放在正方形的中间。这样可以确保这张图片分享出去的图片，无论是被正方形展示还是矩形展示，看起来都不错。



这是此图片在 Twitter 或者 WhatsApp 中的样子。



![Large rectangle image in a Twitter card](https://www.matuzo.at/images/htmldoc_tw500.png)



![Small square image on Whatsapp](https://www.matuzo.at/images/htmldoc_wa500.png)



Twitter 的规则：此卡片图像支持二比一的长宽比，最小尺寸为 300x157，最大尺寸为 4096x4096。图片小于5MB。支持JPG，PNG，WEBP 和 GIF 格式。



- [Twitter Developer Docs: Cards](https://developer.twitter.com/en/docs/twitter-for-websites/cards/overview/summary-card-with-large-image)



```html
<meta property="og:image:alt" content="Image description">
```



图片说明。 如果图片纯粹是装饰性的并且没有提供任何有意义的信息，请不要使用此元标记。 如果我们不提供替代文字，则屏幕阅读器会忽略图像。图片说明。 如果图片纯粹是装饰性的并且没有提供任何有意义的信息，请不要使用此元标记。 如果我们不提供替代文字，则屏幕阅读器会忽略图像。



```html
<meta property="og:locale" content="en_GB">
```



可选的Open Graph属性，但建议使用。 它定义页面的自然语言。



```html
<meta property="og:type" content="website">
```



您要共享的内容类型，例如 网站，文章或video.movie。 有效的“打开图形”页面的必需属性。



```html
<meta property="og:url" content="https://www.mywebsite.com/page">
```



页面的规范URL。 有效打开图形页面所需的属性。



- 开放图协议



```html
<meta name="twitter:card" content="summary_large_image">
```



此元标记定义了在Twitter上共享时卡片的外观。 网站，摘要和summary_large_image有两个选项。



![A large rectangle image on top, followed by the page title, description and URL below.](https://www.matuzo.at/images/htmldoc_summary-large500.png)



![A small square image on the left, page title, description and URL on the right.](https://www.matuzo.at/images/htmldoc_summary500.png)



您可以看到我正在使用正方形图像，以确保卡在两种变化中都好。 我涂上了卡粉红色的顶部和底部，以便您可以看到这些部件将在Sumbase_Large_Image中切断。



图标和地址栏



```html
<meta name="theme-color" content="#FF00FF">
```





主题颜色为具有CSS颜色提供浏览器，以自定义页面的显示或周围的用户界面的显示。



支持的浏览器：Android上的Chrome，Brave和Samsung Internet。



![Pink UI in Brave browser](https://www.matuzo.at/images/htmldoc_theme-color800.jpg)



```html
<link rel="icon" href="/favicon.ico">
```



用于传统浏览器的32×32px Favicon。 它应该生活在您的网站根中。



```html
<link rel="icon" href="/favicon.svg" type="image/svg+xml">
```



大多数现代浏览器支持SVG Favicons。 Favicon.svg的好处是它在缩放时看起来更好，因为它是一个向量而不是光栅图像，我们可以将HTML和CSS添加到SVG，这意味着我们可以支持黑暗模式。



```html
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
<style>
  path {
    fill: #153a51;
  }

  @media (prefers-color-scheme: dark) {
    path {
      fill: #FFFFFF;
    } 
  }
</style>
<path d="M397.8 117.9v290h-76.4V273.5h-.8l-46.4 97.2h-36.8l-46-96.8h-.8v134h-76.4v-290h80.4l60.8 150.8h.8l61.2-150.8h80.4z"/>
</svg>
```



在我的网站上的Favicon在光线模式下。



![A blue M on light background in the browser tab in Chrome](https://www.matuzo.at/images/htmldoc_favicon_light800.png)



在我的网站上的favicon在黑暗模式。



![A white M on dark background in the browser tab in Chrome](https://www.matuzo.at/images/htmldoc_favicon_dark800.png)

