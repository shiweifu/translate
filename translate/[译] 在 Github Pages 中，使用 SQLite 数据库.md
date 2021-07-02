翻译自：[Hosting SQLite databases on Github Pages - (or any static file hoster) - phiresky's blog](https://phiresky.github.io/blog/2021/hosting-sqlite-databases-on-github-pages/)



我写过[一个小网站](https://phiresky.github.io/youtube-sponsorship-stats/?uploader=Adam+Ragusea) ，来显示我关注的 Youtube 视频创作者的赞助内容的统计信息，当我注意到我常常编写一些小工具网站，来将一些数据库中的数据图形化，显示为图片，表格或者类似的玩意儿。但这种场景下，如果你选择使用数据库，你同时需要写一个后端（你也同样需要维护它），或者浏览器下载所有的数据（使用浏览器加载最好不要超过 10MB 的数据。）



过去，当我在这些小项目的服务端中，使用了某些第三方的 API 时，这些外部 API 有可能会关闭服务，或者到期，或者忘记支付部署的 VPS 的费用。当我隔了一年，重新访问这些服务时，他们已经消失了……这让我很生气，诅咒自己所依赖的外部服务，或者在更长时间内，关心我的关心。

