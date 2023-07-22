翻译自：[Dockerizing your Next.js/React Application! - DEV Community](https://dev.to/kaflenitish/dockerizing-your-nextjsreact-application-42ob)

## 什么是 Docker？

Docker是一个用于在服务器和云上构建、运行和管理容器的软件框架。把Docker想象成一个CLI，但适用于云。

在本教程中，我们将使用Next.js示例应用程序并创建一个Dockerfile来对其进行Dockerize。

### 必要条件

为了完成Next.js应用程序的Dockerize，您需要以下内容：

- `Docker` 安装到你的电脑上。

- `Node.js` 和 `npm/yarn`，安装到你的系统中，用于创建新的 App。

### 创建一个示例 `Next.js` app

如果你已经有了一个想要dockerize的应用程序，那么你可以继续执行进一步的步骤，否则让我们创建一个next.js应用程序。

在终端中，执行以下指令：

```
yarn create next-app 
```

此命令将初始化创建和运行 `next.js` 应用程序所需的文件和配置。

### 创建 `Dockerfile`

首先，让我们在VS代码或您选择的任何代码编辑器中打开我们的应用程序。

执行下面的命令：

```
cd <your app name>
code .
```

（假设您已配置vscode）

在这里，您将看到应用程序的目录。这看起来像这样。



[注意：我使用的是TypeScript，这就是您看到tsconfig.json和以.ts结尾的文件的原因]



![file contents](https://res.cloudinary.com/practicaldev/image/fetch/s--EH1dDAQq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pt1050neh8694gdzl9on.png)



继续创建一个新文件，并将其命名为Dockerfile。默认情况下，这个文件由docker识别，它将执行我们将提供的一堆命令和指令。



请记住：命令将按照编写方式的顺序执行。



在Dockerfile中编写这些代码。我将在教程结束时仔细阅读每一个，并解释它是如何工作的。



[注意：我在本教程中使用了yarn，你可以使用npm，但你必须用npm交换那些yarn可执行代码]



```
FROM node:lts as builder
WORKDIR /<your-app-name>
COPY . .
COPY --from=dependencies /<your-app-name>/node_modules ./node_modules
RUN yarn build

FROM node:lts as runner
WORKDIR /<your-app-name>
ENV NODE_ENV production

COPY --from=builder /<your-app-name>/public ./public
COPY --from=builder /<your-app-name>/package.json ./package.json
COPY --from=builder /<your-app-name>/.next ./.next
COPY --from=builder /<your-app-name>/node_modules ./node_modules

EXPOSE 3000
CMD ["yarn", "start"]
```



## 构建 Docker 镜像



执行以下命令来构建Docker镜像。



```
docker build . -t <project-name>
```

该命令将构建名为＜project name＞的Docker镜像。



使用以下命令在构建完成后运行Docker映像。



```
docker run -p 3000:3000 <project-name>
```



现在，打开浏览器并导航到



```
http://localhost:3000 
```



去查看我们的项目。



## 恭喜！您已成功将您的应用程序 Docker 化！



### Dockerfile的访问内容



现在，让我们浏览一下 `Dockerfile` 的代码内容。






