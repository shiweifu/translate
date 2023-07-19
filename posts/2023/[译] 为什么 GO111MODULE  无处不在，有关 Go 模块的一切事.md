翻译自：[Why is GO111MODULE everywhere, and everything about Go Modules (updated with Go 1.20) - DEV Community](https://dev.to/maelvls/why-is-go111module-everywhere-and-everything-about-go-modules-24k)



你可能注意到了，`GO111MODULE=on` 参数无处不在。许多项目的 README 文件，都包含以下内容：

```
GO111MODULE=on go get golang.org/x/tools/gopls@latest
```

注意，安装二进制文件的 `go get` 从 Go 1.17 版以后，已经废弃使用，在 1.18 中，已经不再可用。如果你在使用 Go 1.16 或者以上版本，应该使用：

```
go install golang.org/x/tools/gopls@latest
```

在这篇短文中，我将解释为什么在Go 1.11中引入GO111MODULE，它的注意事项和在处理Go模块时需要了解的有趣之处。

### 从 GOPATH 到 GO111MODULE

首先，我们来谈谈GOPATH。当Go在2009年首次推出时，它并没有附带软件包管理器。相反，go-get将使用它们的导入路径获取所有源，并将它们存储在$GOPATH/src中。没有版本控制，“master”分支将代表包的稳定版本。

Go模块（以前称为vgo，版本为Go）是在Go 1.11中引入的。Go Modules不是使用GOPATH来存储每个包的单位数签出，而是使用Go.mod来存储标记的版本，以跟踪每个包的版本。

从那时起，“ Gopath行为”与“ GO模块行为”之间的相互作用已成为GO的最大陷阱之一。一个环境变量负责这种疼痛的95％：GO111MODULE。



## `GO111MODULE` 环境变量



`GO111MODULE` 是一个环境变量，可以在使用go更改go导入包的方式时进行设置。第一个痛点是，根据Go版本的不同，它的语义会发生变化。



`GO111MODULE` 可以通过两种不同的方式进行设置：



- shell中的环境变量，例如：`export GO111MODULE=on`。

- “隐藏”全局配置只有使用go-env-wGO111MODULE=on的go-env才知道（仅在go 1.12之后可用）。



如果Go似乎按照您认为的方式运行，建议检查您过去是否使用Go env-w GO111MODULE=on（或off）设置了全局配置：
## `go.mod` 的陷阱正在悄悄更新（Go 1.15 及以下）

对于Go 1.15及以下版本，我们安装二进制文件的唯一选择是使用Go get。你可能在项目的 README 文件中，中发现过这样一句奇怪的话：

```
(cd && GO111MODULE=on go get golang.org/x/tools/gopls@latest)
```

> 备注：`@latest` 后缀将使用 `gopl` 最新的 git 标签。请注意，`-u`（更新的意思） 从 `@v0.1.8` 以后，不再是必要的，并且更新固定版本，也没什么意义。有趣的是，如果标签是 `@v0.1`，`go get` 将获取该标签的最新补丁版本。

那么，为什么我们需要使用这个人为的命令，调用一个子命令并移动到你的 HOME？这是Go 1.16修复的另一个Go意识形态：在Go 1.15及以下版本中，默认情况下（你不能关闭它），如果你所在的文件夹中有go.mod，go get会用你刚刚安装的内容更新该 go.mod。在像 [gopls](https://github.com/golang/tools/tree/master/gopls) 或者 [kind](https://github.com/kubernetes-sigs/kind)这样的开发二进制文件的情况下，你肯定不想让这些出现在go.mod文件中！

因此，对于任何使用 go 1.15 及以下版本的人来说，解决方法是提供一行代码，确保你不会在 go.mod-enabled 文件夹中：（cd && go-get）正是这样做的。

幸运的是，在`go 1.16`的情况下，现在可以清楚地分离出`go.mod`（例如 `npm install`）的依赖性，并且`go`安装旨在安装二进制，而无需弄乱您的`go.mod`。使用`go 1.16`，上述过程成为：

```
go env GO111MODULE
```

## `-u` 和 `@version` 缺陷

我多次陷入过同一个陷阱：`go get @latest`（获取最新版本的二进制文件），你应该避免使用 `-u`，以便它使用 `go.sum` 中的定义。否则，它会将所有依赖，更新为最新版本。并且，由于大量项目小版本之间，会有破坏性更新（如v0.2.0到v0.3.0），因此，使用 `-u` 参数，很有可能搞砸。

请注意，环境变量优先于使用 `go env -u  GO111MODULE` 存储的值。
如果你看到下面：

```
# Both -u and @latest!
GO111MODULE=on go get -u golang.org/x/tools/gopls@latest
```

你可以立即确认两点：1. 它使用的是老版本的 Go，早于 1.16 版本。2. 当获取二进制文件时，你会获取 `go.sum` 文件中，记录的二进制版本。

要取消设置全局配置，可以执行以下操作：
> -u不应该与@latest标记一起使用，因为它会给您提供不正确的依赖关系版本。

但它有点隐藏在这个问题中。。。我想它写在Go帮助的某个地方（顺便说一句，与git help相比，这是一个多么可怕的帮助），但这种警告应该更明显：也许在安装带有@version和-u的二进制文件时会打印警告？


```
go env -u GO111MODULE
```
## 使用 Go Modules 时的注意事项

现在，让我们来了解一下使用 Go 模块时的一些注意事项。

### 记住，`go get` 一直会更新 `go.mod`


### `GO111MODULE` 在 Go 1.11 和 1.12
在 go 1.16 出现之前，go get 有一个奇怪的地方：有时，它的目的是安装二进制文件或下载包。有了 Go 模块，如果你在一个有 go.mod 的项目中，它会悄悄地将你要去的二进制文件添加到你的 go.mod 中！

幸运的是，Go 1.16 之后，`go install` [引入了](https://blog.golang.org/go116-module-changes) `@version` 前缀。通过 `go install foo@version`，你本地的 `go.mod` 不会受影响！在 Go 1.18 中，`go get` 不会再安装二进制文件，并且只会将依赖添加到您的 `go.mod` 中。

如果你想运行 `go test` 或者 `go install`，但不想更新 `go.mod`，你可以使用 `-mod=readonly` 参数。举个例子：


- `GO111MODULE=on` 将强制使用Go模块，即使项目在您的GOPATH中也是如此。需要go.mod才能工作。

- `GO111MODULE=off`强制以GOPATH的方式行事，即使在GOPATH之外。

- `GO111MODULE=auto`是默认模式。在此模式下，Go将执行：
  
  - 类似于`GO111MODULE=on`，当您在GOPATH之外时打开，
  
  - 类似于`GO111MODULE=off`，当您在GOPATH内部时，即使存在go.mod。



每当您进入Gopath并想执行需要GO模块的操作（例如，获取特定版本的二进制版本）时，您需要做：
```
go test -mod=readonly ./...
go build -mod=readonly ./...
```

### Go 模块依赖关系的保存地址在哪里？

使用Go模块时，Go构建过程中使用的软件包存储在$GOPATH/pkg/mod中。当试图检查vim或VSCode中的“import”时，您可能会得到该包的GOPATH版本，而不是编译过程中使用的pkg/mod版本。

出现的第二个问题是，当您想修改其中一个依赖项时，例如出于测试目的。

`方案1`：使用 `go mod vendor` + `go build mod=vendor`。这将强制 go 使用 vendor/ 文件，而不是使用 `$GOPATH/pkg/mod` 文件。此选项还解决了 `vim` 和 `VSCode` 无法打开包文件的正确版本的问题。

`方案2`：添加 'replace' 行，在 `go.mod` 文件中：

```
replace github.com/maelvls/beers => ../beers
```

其中`../beers` 是我想检查和修改的依赖项的本地副本。

## 使用 direnv 按文件夹设置GO111MODULE

在旧版本的Go（1.15及以下）上，当我从基于GOPATH的项目（主要使用Dep）迁移到Go模块时，我发现自己在两个不同的地方挣扎：GOPATH内部和外部。所有Go模块都必须保存在`GOPATH` 之外，这意味着我的项目位于不同的文件夹中。为了补救这个问题，我广泛使用了`GO111MODULE`。我会将我的所有项目都保存在GOPATH中，对于启用Go模块的项目，我会将导出 `GO111MODULE=on`  为打开。

> 注意：由于Go 1.16中的默认行为现在是GO111MODULE=on，因此不再需要此技巧。

这就是 [`direnv`](https://direnv.net/) 派上用场的地方。direnv 是一个用Go编写的轻量级命令，每当您输入目录并且.envrc存在时，它都会加载一个文件.envrc。对于每个启用Go模块的项目，我都会有以下.envrc：



```
# .envrc
export GO111MODULE=on
export GOPRIVATE=github.com/mycompany/\*
export GOFLAGS=-mod=vendor
```



## 私有Go模块和Dockerfile



许多公司选择使用私有存储库作为导入路径。如上所述，我们可以使用 `GOPRIVATE` 来告诉Go（从Go 1.13开始）跳过包代理，直接从Github获取我们的私有包。



但是构建Docker镜像呢？如何从docker构建中获取我们的私人存储库？



### 方案1：vendoring



通过 `go mod vendor`，不需要传递 Github 凭据，传递给 docker 构建上下文。我们可以将所有的内容，都放在 `vendor/` 中，此时问题解决。在 Dockerfile 中，`-mod=vendor` 是必须的，但开发者甚至不必通过 `-mod=vendor`，因为他们可以使用其本地 git配置：



- Pros：通过 CI，快速构建（减少10秒到30秒）

- Cons：PRs 有可能因为 `vendor/` 改变，repo 的尺寸，也许会变大



### 方案2：不使用 vendoring



如果 `vendor/` 的尺寸太大（例如，对于Kubernetes控制器，`vendor/` 大约为30MB），我们可以很好地在不进行 vendoring 的情况下完成它。这需要传递某种形式的 `GITHUB_TOKEN` 作为 `docker` 构建的参数，并在 `Dockerfile` 中设置如下内容：



```
GO111MODULE=on go get github.com/golang/mock/tree/master/mockgen@v1.3.1
git config --global url."https://foo:${GITHUB_TOKEN}@github.com/company".insteadOf "https://github.com/company"
export GOPRIVATE=github.com/company/\*

```
