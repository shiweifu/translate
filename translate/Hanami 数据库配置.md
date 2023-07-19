Hanami 数据库配置



#### 环境变量



在开始服务前，你需要在 `.env*` 文件中，配置数据库链接。



打开与环境相对应的文件，为数据库更新更新 `DATABASE_URL` 的配置。



为开发环境，配置数据库变量：

```
# .env.development
DATABASE_URL="database_type://username:password@localhost/bookshelf_development"
```



为测试环境，配置数据库变量：

```
# .env.test
DATABASE_URL="database_type://username:password@localhost/bookshelf_test"
```



对于 jdbc 链接，你不能在 `@` 左边，设置用户名和密码，你必须设置 url 参数：

```
DATABASE_URL="jdbc-database_type://localhost/bookshelf_test?user=username&password=password"
```



#### 设置你的数据库



配置了数据库变量后，你需要创建所要使用的数据库，并执行合并，然后再运行开发服务器。



在终端中，执行：



```shell
$ bundle exec hanami db prepare
```



要配置 test 环境下的数据库，输入：



```shell
$ HANAMI_ENV=test bundle exec hanami db prepare
```



#### Sequel 插件



Hanami models 使用 ROM 作为底层实现。这意味着你可以容易的在你的应用程序中，使用任何 Sequel 插件。要实现这个操作，你需要在你的模型配置文件中，定义 `gateway` 块，通过调用 `gateway.connection` 的 `extension` 方法，并传递扩展名字：



```
# config/environment.rb

Hanami.configure do
  model do
    gateway do |g|
      g.connection.extension(:connection_validator)
    end
  end
end
```





