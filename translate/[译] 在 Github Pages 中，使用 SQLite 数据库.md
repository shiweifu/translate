翻译自：[Hosting SQLite databases on Github Pages - (or any static file hoster) - phiresky's blog](https://phiresky.github.io/blog/2021/hosting-sqlite-databases-on-github-pages/)



我写过[一个小网站](https://phiresky.github.io/youtube-sponsorship-stats/?uploader=Adam+Ragusea) ，来显示我关注的 Youtube 视频创作者的赞助内容的统计信息，当我注意到我常常编写一些小工具网站，来将一些数据库中的数据图形化，显示为图片，表格或者类似的玩意儿。但这种场景下，如果你选择使用数据库，你同时需要写一个后端（你也同样需要维护它），或者浏览器下载所有的数据（使用浏览器加载最好不要超过 10MB 的数据。）



过去，当我在这些小项目的服务端中，使用了某些第三方的 API 时，这些外部 API 有可能会关闭服务，或者到期，或者忘记支付部署的 VPS 的费用。当我隔了一年，重新访问这些服务时，他们已经消失了……这让我很生气，诅咒自己所依赖的外部服务，或者在更长时间内，担忧着这些事情。



构建一个静态的网站，比一个”真实“的服务端，要简单很多 - 有许多开箱即用的选项（Github、Gitlab Pages，Netlify 等等），并且它们基本没什么限制，可以自由使用。



所以我写了一个工具，在编写这些小型的统计服务时，可以在静态环境种，使用 SQL 数据库！



这是一个例子 [World Development Indicators dataset](https://github.com/phiresky/world-development-indicators-sqlite/) - 这个数据库中，有六个表，超过八百万条数据（总共670 M 数据）。



```
select country_code, long_name from wdi_country limit 3;
```

```
[
  {
    "country_code": "ABW",
    "long_name": "Aruba"
  },
  {
    "country_code": "AFG",
    "long_name": "Islamic State of Afghanistan"
  },
  {
    "country_code": "AGO",
    "long_name": "People's Republic of Angola"
  }
]
```

```
fetched 1.0KB in 1 requests (DB size: 668.8MB)
```



如你所见，我们可以查询 `wdi_country` 表，来获取 1kB 的数据！



这是一个完整的 SQLite 引擎，因此，我们也可以使用  [SQLite JSON functions](https://www.sqlite.org/json1.html)：

```
select json_extract(arr.value, '$.foo.bar') as bar
  from json_each('[{"foo": {"bar": 123}}, {"foo": {"bar": "baz"}}]') as arr
```

```
[
  {
    "bar": 123
  },
  {
    "bar": "baz"
  }
]
```













