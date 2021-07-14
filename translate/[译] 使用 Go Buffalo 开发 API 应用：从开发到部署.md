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

