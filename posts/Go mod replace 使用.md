Go mod replace 使用



日常开发离不开第三方库，大部分的时候，这些库都是满足我们的需要，但有的时候，我们需要 fork 一份，做一些修改。go mod 作为当前 go 语言的官方包管理器，自然也考虑到了这种情况。在 go.mod 文件中，通过 replace 指令，将旧的库地址，替换为新的库地址来实现这一操作。



下面通过一个示例来讲解 go replace 的使用，以及常见问题的处理。



我们首先新建一个项目，并在其中引用 [ozgio/strutil: String utilities for Go (github.com)](https://github.com/ozgio/strutil) 这个字符串处理库，然后随便写段代码，确保其可以正常工作：



```
package main

import (
	"fmt"

	"github.com/ozgio/strutil"
)

func main() {
	fmt.Println(strutil.Align("lorem ipsum", strutil.Right, 20))
}
```





### go mod 的初始化



`go mod init project_name`



go mod init 命令执行后，会自动生成 `go.mod` 文件，该文件中，列出了项目所依赖的第三方包，以及所使用的版本。



然后执行 `go mod tidy`，该命令做两件事：



1. 解析项目文件，并找到所使用的包
2. 生成 go.sum 文件，其中保存了所使用包的版本



然后执行 `go run main.go`，来执行项目。

现在，项目应该已经可以正常执行，并返回执行结果了。



假设我们此时想调用一个过滤字符串中，HTML 标签的方法，但翻了一下并没有，于是 fork 了一份这个库，我们自己添加了进去：



```
https://github.com/shiweifu/strutil/blob/master/escape.go
```



下面我们来看如何调用这个新的方法。



第一种方式：

1. fork strutil 库，打开 go.mod 文件，将第一行中的 module name 修改为一个新的 name
2. 增加所需要的方法
3. 增加新的 git tag
4. 在你当前项目中，引用你修改后的这个 repo，替换地址以及版本号



这种方式相当于引用了一个新的库，与之前那个库已经没有什么关系了。大多数时候，因为对代码修改过多，我们并不会想要这么用。go mod 当然也考虑到了这一点，go mod 提供了 go mod replace 方法来应对这种场景。



第二种方式：

1. fork strutil 库
2. 增加所需要的方法
3. 在当前项目中，执行 `go mod edit -replace` 命令：

```
go mod edit -replace [old git package]@[version]=[new git package]@[version]
```



执行完命令后，我们打开 `go.mod` 文件，发现最下面多了一条指令：



```
replace github.com/ozgio/strutil v0.3.0 => github.com/shiweifu/strutil v0.3.0
```



go mod replace 指令支持指定版本号，可以为 git tag，也可以为 git commit 日期 + git commit hash 的组合。



可以通过以下指令，来获取某个分支的最新版本：



```
go get github.com/shiweifu/strutil@master
```



此时会输出 `master` 分支的最新 commit 记录：



```
github.com/shiweifu/strutil@v0.3.1-0.20210615145512-3bd39e22cb0d 
```



把这段版本号复制到 `go.mod` 文件`replace` 指令，将对应的版本号替换成这个，然后再次执行，就可以使用我们自己 fork 的 strutil 了：



```
package main

import (
	"fmt"

	"github.com/ozgio/strutil"
)

func main() {
	out := strutil.EscapeHTMLTag("<script>abc</script>")
	fmt.Println(out)
}
```



我们引用的还是 `github.com/ozgio/strutil` 这个库，而 `EscapeHTMLTag` 是我们新添加的方法，这种方式只是对 `go.mod` 进行了修改，然后我们可以对 `ozgio/strutil` 提一个 pr，如果我们的代码被合并进仓库，我们可以把 `replace` 语句给删除掉，这种方式没有破坏原有的代码。

