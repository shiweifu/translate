翻译自：[Linux Terminal: The Ultimate Cheat Sheet - Part 2 - DEV Community](https://dev.to/mauro_codes/linux-terminal-the-ultimate-cheat-sheet-part-2-11ge)



这篇文章是本系列文章的第二部分，将帮助您了解如何从Linux终端命令中获益。



如果你没有看到我的第一篇文章，我强烈建议你在继续这篇文章之前先看一看：

https://dev.to/mauro_codes/linux-terminal-the-ultimate-cheat-sheet-2g5b



💡 如果你觉得这些内容很有价值，你可以在 [Twitter](https://twitter.com/mauro_codes) 和  [Instagram](https://www.instagram.com/mauro.codes/) 上关注我。



## TL;DR



**查找字符串**

* 在文件内容中搜索字符串➜ `grep [term-to-search] [source-file-to-search]`
- 在文件内容中搜索，包含大小写 ➜ `grep -i [term-to-search] [source-file-to-search]`
- 在文件内容中搜索不匹配的内容 ➜ `grep -v [term-to-search] [source-file-to-search]`
- 递归搜索目录 ➜ `grep -r [term-to-search] [path-to-directory-to-search]`
- 多个搜索用例 ➜ `grep -E "[first-term-to-search|second-term-to-search]" [source-file-to-search]`
- 统计搜索结果 ➜ `grep -c [term-to-search] [source-file-to-search]`
- 显示匹配的文件名 ➜ `grep -l [term-to-search] [matching-files-to-search]`
- 了解更多有关 `grep`的内容 ➜ `man grep`



## Pipes



- 管道命令 ➜ `[command 1] | [command 2] | [command n]`
- 管道过滤搜索结果到一个新文件 ➜ `ls | grep [term-to-filter] | cat > [path-to-new-file]/[name-for-new-file]`
- 过滤搜索历史到管道 ➜ `history | grep "[term-to-search]"`



## 权限控制：修改文件的位控制命令（chmod）



- 将可执行权限赋予一个文件 ➜ `chmod a+x [name-of-the-file]` or `chmod +x [name-of-the-file]`
- 移除文件的可执行权限 ➜ `chmod a-x [name-of-the-file]` or `chmod -x [name-of-the-file]`
- 添加可执行权限到文件的拥有者 ➜ `chmod u+x [name-of-the-file]`
- 对其他用户，移除可执行权限 ➜ `chmod o-w [name-of-the-file]`
- 对当前组，添加可执行权限 ➜ `chmod g+r [name-of-the-file]`
- 移除所有人的可执行权限 ➜ `chmod a-wr [name-of-the-file]`
- 移除所有人对当前目录中所有文件的写入和读取权限 ➜ `chmod a-wr *.*`




