Go mod replace 使用



日常开发离不开第三方库，大部分的时候，这些库都是满足我们的需要，但有的时候，我们需要 fork 一份，做一些修改。go mod 作为当前 go 语言的官方包管理器，自然也考虑到了这种情况。在 go.mod 文件中，通过 replace 指令，将旧的库地址，替换为新的库地址来实现这一操作。



下面通过一个示例来讲解 go replace 的使用，以及常见问题的处理。



我们首先新建一个项目，并在其中引用 [ozgio/strutil: String utilities for Go (github.com)](https://github.com/ozgio/strutil) 这个字符串处理库，然后随便写段代码，确保其可以正常工作：



```
func main() {
	fmt.Println("asdf")
}
```

