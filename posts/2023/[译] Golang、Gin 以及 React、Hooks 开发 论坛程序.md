翻译自：[Forum App with Golang/Gin and React/Hooks - DEV Community](https://dev.to/stevensunflash/real-world-app-with-golang-gin-and-react-hooks-44ph)



您是否一直期待使用Golang和React构建生产应用程序？这就是一个。



此应用程序有一个API后端和一个使用API的前端。



该应用程序分散在两个项目中：



- https://github.com/victorsteven/Forum-App-Go-Backend (后端)
- https://github.com/victorsteven/Forum-App-React-Frontend (前端)



这是应用程序的线上版本。你可以和它交互。



- [https://seamflow.com](https://seamflow.com/)



### 所用工具



后端工具：



- Golang
- Gin Framework
- GORM
- PostgreSQL/MySQL



前端工具：



- React
- React Hooks
- Redux



 部署工具：



- Linux
- Nginx
- Docker



虽然以上内容可能看起来来势汹汹，但你会看到它们是如何协同工作的。



你可能还想在这里查看我关于go、docker、kubernetes的[其他文章](https://medium.com/@victorsteven)。



### 第1节：构建后端



这是与Golang连接的后端会话。



在这里，我将逐步介绍所做的工作。



#### 第一步：基本设置



##### a. 基础目录



在计算机中选择的任何路径上创建 `forum` 的目录，然后切换到该目录：



```
        ```mkdir forum && cd forum```
```



##### b. Go Modules



初始化 `go module`。这就考虑到了我们的依赖性管理。在根目录中运行：



`go mod init github.com/victorsteven/forum`


