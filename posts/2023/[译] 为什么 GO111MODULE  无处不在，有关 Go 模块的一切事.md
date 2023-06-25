翻译自：[Why is GO111MODULE everywhere, and everything about Go Modules (updated with Go 1.20) - DEV Community](https://dev.to/maelvls/why-is-go111module-everywhere-and-everything-about-go-modules-24k)



你可能注意到了，`GO111MODULE=on` 无处不在。许多 README，包含以下内容：



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
