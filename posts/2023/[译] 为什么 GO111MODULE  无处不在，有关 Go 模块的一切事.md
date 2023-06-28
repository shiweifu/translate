翻译自：[Why is GO111MODULE everywhere, and everything about Go Modules (updated with Go 1.20) - DEV Community](https://dev.to/maelvls/why-is-go111module-everywhere-and-everything-about-go-modules-24k)

你可能注意到了，`GO111MODULE=on` 无处不在。许多 README，包含以下内容：

```
注意，安装二进制文件的 `go get` 从 Go 1.17 版以后，已经废弃使用，在 1.18 中，已经不再可用。如果你在使用 Go 1.16 或者以上版本，应该使用：
```

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



```
go env GO111MODULE
```



请注意，环境变量优先于使用 `go env -u  GO111MODULE` 存储的值。



要取消设置全局配置，可以执行以下操作：



```
go env -u GO111MODULE
```



### `GO111MODULE` 在 Go 1.11 和 1.12



- `GO111MODULE=on` 将强制使用Go模块，即使项目在您的GOPATH中也是如此。需要go.mod才能工作。

- `GO111MODULE=off`强制以GOPATH的方式行事，即使在GOPATH之外。

- `GO111MODULE=auto`是默认模式。在此模式下，Go将执行：
  
  - 类似于`GO111MODULE=on`，当您在GOPATH之外时打开，
  
  - 类似于`GO111MODULE=off`，当您在GOPATH内部时，即使存在go.mod。



每当您进入Gopath并想执行需要GO模块的操作（例如，获取特定版本的二进制版本）时，您需要做：



```
GO111MODULE=on go get github.com/golang/mock/tree/master/mockgen@v1.3.1
```
