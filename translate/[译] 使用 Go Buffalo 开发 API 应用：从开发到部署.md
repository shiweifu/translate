翻译自：[API With GO Buffalo in 2021: from zero to deploy - DEV Community](https://dev.to/alexmercedcoder/api-with-go-buffalo-in-2021-from-zero-to-deploy-5642)



在 Web Server 和 微服务领域，Go 语言成为了一个流行的选择。Buffalo 是一个开发框架，允许使用者以类似 Ruby on Rails 的方式，使用 Go 语言来进行开发。在本教程中，我们将使用 Buffalo，编写一个简单的 API 服务，并将其分发到 Heroku。



### 准备



- 安装好 Go 语言
- 安装好 Buffalo
- Postgres 数据库



### 终端说明



| 命令                                          | 解释                         |
| :-------------------------------------------- | :--------------------------- |
| buffalo new {projectname} --api               | 创建新的 API 模板项目        |
| buffalo dev                                   | 运行开发服务器               |
| buffalo pop create -a                         | 创建 database.yml 中的数据库 |
| buffalo pop drop -a                           | 删除 database.yml 中的数据库 |
| buffalo pop generate fizz {name of migration} | 创建新的合并                 |
| buffalo pop migrate                           | 执行合并操作                 |
| buffalo pop g model {model name}              | 生成模型文件                 |



### 初始化



- 通过命令 `buffalo new project1 --api` 创建新的项目，`--api` 标记的意思和  Rails 中一样，加了这个标记，表示将使用针对 API 优化过的模板
- `cd` 进入新的项目中，使用编辑器打开文件夹
- 运行 `buffalo dev`，开发模式下执行项目



#### 注意



在 `Go 1.16` 或者更高版本中，使用 `buffalo 0.16.21` 或者更早的版本，将会有一些问题需要注意。



- 如果在执行时，询问你是否获一些新的类库时，请照提示做。我认为这是因为 go 1.16 之前，安装并不会自动添加模块到 `go.mod`，所以，你需要手动修复这个问题。

```
go get github.com/gobuffalo/envy@v1.9.0
go get github.com/gobuffalo/pop/v5@v5.3.0
go get github.com/gobuffalo/packr/v2@v2.8.0
go get github.com/rs/cors
go get github.com/gobuffalo/buffalo-pop/v2/pop/popmw@v2.3.0
go get github.com/gobuffalo/validate/v3@v3.1.0
go get github.com/gofrs/uuid@v3.2.0+incompatible
go get
go mod tidy
```

- 如果您收到错误的SQLite3将此导出到您的环境中应解决问题，则此似乎不是Go或Buffalo问题，而是关于LibsQlite3和GCC编译器的特定版本的问题。`export CGO_CFLAGS="-g -O2 -Wno-return-local-addr"`
- 如果收到Packr的错误，请务必在操作/ app.go“github.com/gobuffalo/packr/v2中的导入语句中添加以下内容。



### 设置数据库



我们使用 Postgres 数据库，所以我们需要在 `database.yml` 文件中，设置有关数据库的内容。



```
development:
  dialect: postgres
  database: project1_development
  user: test5
  password: test5
  host: 127.0.0.1
  pool: 5

test:
  url: {{envOr "TEST_DATABASE_URL" "postgres://test5:test5@127.0.0.1:5432/project1_test?sslmode=disable"}}

production:
  url: {{envOr "DATABASE_URL" "postgres://test5:test5@127.0.0.1:5432/project1_production?sslmode=disable"}}
```



你的 postgres 设置也许不同，确保你的数据库用户名和密码所设置的内容，具有相关的权限。



合并我们的 Todo Table



第一步，让我们设置我们的 Todos。









