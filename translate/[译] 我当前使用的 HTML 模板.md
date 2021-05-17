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

