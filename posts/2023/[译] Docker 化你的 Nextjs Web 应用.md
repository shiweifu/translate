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

记住，代码的执行是基于它们的编写方式，自上而下的方法。

让我们以自上而下的方法分三个不同阶段来研究代码：

1. 安装依赖

2. 构建我们的 `Next.js` 应用

3. 配置我们环境的运行时

### 1. 安装依赖

```
FROM node:lts as dependencies
WORKDIR /<your-app-name>
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile
```

让我们来谈谈这个代码上发生了什么。

首先，我们想定义我们要从中构建的映像，我们使用node:lts的最新node版本

您可以使用任何特定版本的节点。例如：FROM node:16将使用node版本16构建您的映像。我们使用作为依赖项，这样我们就可以导出这些代码，并在稍后用docker构建应用程序时重用它。

其次，我们想创建一个应用程序目录，其中包含我们使用WORKDIR的应用程序代码。

第三，我们想复制我们的package.json和yarn.lock文件，这使我们能够利用缓存的Docker层。Docker缓存的一个很好的解释在这里。

最后，我们希望能够运行我们的yarn安装来安装这些依赖项。我们使用--freezed-lockfile是因为我们的yarn.lock或package-lock.json在运行yarn install（或npm install）时会更新。我们不想检查这些更改。

如果你正在使用npm，你可以使用npm-ci（ci意味着干净安装/用于生产，或者只使用RUN npm安装）

对于 `yarn`，该参数是 `--frozen-lockfile`

## 构建我们的 `Next.js` 应用

```
FROM node:lts as builder
WORKDIR /<your-app-name>
COPY --from=dependencies /<your-app-name>/node_modules ./node_modules
RUN yarn build
```

让我们看一下构建。

在这里，我们构建了从node_modules复制依赖关系的应用程序。

如果您使用的是npm，那么请使用RUN npm构建。

```
FROM node:lts as runner
WORKDIR /<your-app-name>
ENV NODE_ENV production
```

在建立了我们的项目之后，我们希望能够运行它。

## 3. 配置我们应用的运行时环境

```
COPY --from=builder /<your-app-name>/public ./public
COPY --from=builder /<your-app-name>/package.json ./package.json
COPY --from=builder /<your-app-name>/.next ./.next
COPY --from=builder /<your-app-name>/node_modules ./node_modules

EXPOSE 3000
CMD ["yarn", "start"]
```

在这里，我们希望能够将应用程序源代码捆绑在Docker映像中，这就是我们使用COPY的原因。

对于我们的运行时环境，我们使用yarn命令。

如果你安装了Docker应用程序，你可以在仪表板上查看你的容器，并从那里运行它，这看起来像下面的图像。

![Docker Dashboard](https://res.cloudinary.com/practicaldev/image/fetch/s--kf5rRphY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sgn0i3xa4v26bodc1lhq.png)

![Docker Dashboard](https://res.cloudinary.com/practicaldev/image/fetch/s--VHQjU2bD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_800/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m6pndsjc57mq4qfu7wqp.png)

到此结束。



感谢阅读，如果您有任何问题，请直接联系我 [@developernit](https://twitter.com/developernit)。


