使用 GoFrame 开发博客系统



Golang 的 Web 开发框架并不少，在 Github 上，随便在一搜，就能搜到一堆。上万 Star 的框架，一只手都数不过来。然而这些框架大部分都是【微】框架，体积轻量，功能少，执行快速。但项目需求一复杂，难免会将各种轮子安在上面，然后写一大堆胶水代码。



GoFrame 与这些项目不同，他更强调【简单化】【一致性】【功能丰富】。基本上能想到的应用场景和组件，他都已经具备了，此外他的作者是国人，有一份完整而详细的中文文档。



本文通过编写一个博客系统，来讲解使用 GoFrame 开发【传统】的全栈项目。



### 初步目标定义



- 日志的增删改查
- Tag 支持
- Parcel 打包资源
- 基础的统计信息
- 编辑器支持 markdown，拖拽上传图片



### 成品案例



完整源码地址：



下面逐步说各部分的实现。



博客系统，最核心的当然是数据定义。gf 的 ORM 组件是自己开发的，常用的数据库都支持。数据库的配置在 `config.toml` 文件中，MySQL 示例：



详细配置项：



```
[database]
    [[database.分组名称]]
        host                 = "地址"
        port                 = "端口"
        user                 = "账号"
        pass                 = "密码"
        name                 = "数据库名称"
        type                 = "数据库类型(mysql/pgsql/mssql/sqlite/oracle)"
        link                 = "(可选)自定义数据库链接信息，当该字段被设置值时，以上链接字段(Host,Port,User,Pass,Name)将失效，但是type必须有值"         role                 = "(可选)数据库主从角色(master/slave)，不使用应用层的主从机制请均设置为master"
        debug                = "(可选)开启调试模式"
        prefix               = "(可选)表名前缀"
        dryRun               = "(可选)ORM空跑(只读不写)"
        charset              = "(可选)数据库编码(如: utf8/gbk/gb2312)，一般设置为utf8"
        weight               = "(可选)负载均衡权重，用于负载均衡控制，不使用应用层的负载均衡机制请置空"
        maxIdle              = "(可选)连接池最大闲置的连接数"
        maxOpen              = "(可选)连接池最大打开的连接数"
        maxLifetime          = "(可选)连接对象可重复使用的时间长度"
        createdAt            = "(可选)自动创建时间字段名称"
	    updatedAt            = "(可选)自动更新时间字段名称"
	    deletedAt            = "(可选)软删除时间字段名称"
        timeMaintainDisabled = "(可选)是否完全关闭时间更新特性，true时CreatedAt/UpdatedAt/DeletedAt都将失效"
```



官方推荐使用 link 这种简化方式来配置，MySQL 配置示例：



```
[database]
    link = "mysql:root:12345678@tcp(127.0.0.1:3306)/test"
```



当然也支持不同环境的不同配置：



```
[database]
    [[database.default]]
        link = "mysql:root:12345678@tcp(127.0.0.1:3306)/test"
    [[database.user]]
        link = "mysql:root:12345678@tcp(127.0.0.1:3306)/user"
```





详细的配置文档：

[ORM使用配置 - GoFrame官网 - 类似PHP-Laravel, Java-SpringBoot的Go企业级开发框架](https://goframe.org/pages/viewpage.action?pageId=1114245)









