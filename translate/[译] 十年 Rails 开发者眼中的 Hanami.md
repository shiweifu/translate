翻译自：[Using Hanami after a decade building Rails apps - DEV Community](https://dev.to/mculp/using-hanami-after-a-decade-building-rails-apps-4jnl)



我清楚的记得，第一次看到 DHH 的博客中，15 分钟的视频时我在哪里。那时我总是使用 PHP 开发我的项目，而那个视频令我印象深刻。



![image-20210921000215004](C:\Users\shiweifu\AppData\Roaming\Typora\typora-user-images\image-20210921000215004.png)



我立刻对 Ruby on Rails 充满了幻想。接下来的两年，我主要工作是使用 Java，业余时间我都在学习 Ruby on Rails。最终，在我学习到了足够多的内容后，我的职业生涯从 Java 跳转到 Rails，接下来的十年，我都在致力于 Rails 程序开发。



最近，我有一个业余项目需要开发，我决定使用 Hanami 替代 Rails，来构建它。我选择使用 Hanami，有以下几个理由：



1. 我有了额外的可以自由支配的时间，来学习新东西
2. 我的 Rails 应用，最近趋向于使用大量小规模的的服务对象和 PORO 构建
3. Hanami 的应用架构，像是从 Rails 发展而来



接下来，本文将为你展示一些我所了解的 Hanami，以及我喜欢的部分。



#### 项目



密西西比周，每日提供表格，其中包含每日新增的 COVID 病例，以及死亡人数。



![MSDH table](https://res.cloudinary.com/practicaldev/image/fetch/s--al45FuLW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/s2jy9y2g2nthtfpxlu4n.png)



这张表格图中，只展示了每日增加的人数以及死亡的人数，你无法在其中感受到各个地区的数据的增长。



我决定爬取这个表格，并且减去昨日的数据，以获得每日最新的数字。



![Screenshot of my project](https://res.cloudinary.com/practicaldev/image/fetch/s--keo1WzLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/whapy4597ijxpvuaxrwj.png)







