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



我们也可以注册 JS 方法，他们可以被查询语句调用。下面通过 `getFlag` 方法，来说明一下，该方法获取某个国家的相关 `emoji`：

```
function getFlag(country_code) {
  // just some unicode magic
  return String.fromCodePoint(...Array.from(country_code||"")
    .map(c => 127397 + c.codePointAt()));
}

await db.create_function("get_flag", getFlag)
return await db.query(`
  select long_name, get_flag("2-alpha_code") as flag from wdi_country
    where region is not null and currency_unit = 'Euro';
`)
```

此时，该网站完全部署在静态服务环境中（GitHub Pages）。



那么，如何实现在静态服务的环境中使用数据库呢？首先，SQLite（C语言编写）由 WebAssembly进行编译。SQLite 不需要任何修改，即可被 [emscripten](https://emscripten.org/) 编译， 此外，[sql.js](https://github.com/sql-js/sql.js/) 是使用 wasm 的 JS 封装。



sql.js 只允许你创建和读取内存数据库，所以，我又实现了一个虚拟文件系统，当 SQLite 尝试从文件系统中读取数据的时候，可以通过这个虚拟系统，经由 HTTP 来获取数据库的数据块：[sql.js-httpvfs](https://github.com/phiresky/sql.js-httpvfs)。对 SQLite 而言，它和在普通文件系统中读取数据，没什么两样，除了数据库文件名为 `/wdi.sqlite3`。当然，它不能写入数据库文件，但单单从数据库读取数据就很有用啦。



由 HTTP 加载数据库文件，开销巨大，我们需要以块为单位，获取数据，并在请求数量和所使用的带宽之间，找到平衡。幸运的是，SQLite 已经使用用户定义的页面大小，将其数据库组织为 “pages”。对于此数据库，我将设置页大小为 1 KiB。



下面是一个查找索引的例子：

```
select indicator_code, long_definition from wdi_series where indicator_name
    = 'Literacy rate, youth total (% of people ages 15-24)'
```



运行上面的查找例子，SQLite 在此查找中，找到了 7 个页。

- 三页读取只是获取一些模式信息(这些信息已经被缓存了)
- 两页读取是在wdi_series (indicator_name)上的索引中查找索引
- 对wdi_series表数据进行两次页读取(第一次是根据主键查找行值，第二次是从溢出页获取文本数据)



索引和表读取都是使用 B-Tree 进行查找。



一个更复杂的问题是：根据2010年后的最新数据，哪些国家的青年识字率最低?





