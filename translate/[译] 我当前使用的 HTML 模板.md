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