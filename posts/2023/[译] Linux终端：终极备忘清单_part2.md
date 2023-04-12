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



## 群组



- 列出全部可用的组 ➜ `getent group`
- 列出当前账号，全部所在的组 ➜ `groups`
- 搜索一个指定的组（使用pipes）➜ `getent group | grep [group-name-to-search]`
- 创建新的组 ➜ `sudo groupadd [name-for-the-new-group]`
- 将用户添加到另一个已经存在的组 ➜ `usermod -a -G [group-you-want-to-add-the-user-to] [user-name-to-add]`



## 拥有权限：更改文件拥有者和组（chown）



- 更改一个文件的拥有者 ➜ `sudo chown [new-owner-name] [file-to-change-ownership]`
- 更改数个文件的拥有者 ➜ `sudo chown [new-owner-name] [file-1-to-change-ownership] [file-n-to-change-ownership]`
- 更改一个目录的拥有者 ➜ `sudo chown [new-owner-name] [directory-to-change-ownership]`
- 递归修改一个目录及其中所有的文件的拥有者 ➜ `sudo chown -R [new-owner-name] [directory-to-change-ownership]`
- 更改一个文件的拥有者 ➜ `sudo chown :[new-group-name] [file-to-change-ownership]`
- 更改一个文件的用户和群组拥有者 ➜ `sudo chown [new-owner-name]:[new-group-name] [file-to-change-ownership]`



## 快捷键



- 搜索历史记录 ➜ `[CTRL] + r`. 然后键入要搜索的命令片段
- 粘贴前一行的内容 ➜ `[CTRL] + p`
- 移动光标到当前命令行行首 ➜ `[CTRL] + a`
- 移动光标到当前命令行行尾 ➜ `[CTRL] + e`
- 移动坐标，向前一个字符 ➜ `[CTRL] + f`
- 移动坐标，向后一个字符 ➜ `[CTRL] + b`
- 清空当前已经完成的行 ➜ `[CTRL] + u`
- 清空最后一个单词 ➜ `[CTRL] + w`



## 长文件的一些操作方法



- 打印一个文件的最后几行 ➜ `tail [name-of-the-file]`
- 打印一个文件的最后 N 行 ➜ `tail -n [number-of-lines] [name-of-the-file]`
- 打印一个文件的开始几行 ➜ `head [name-of-the-file]`
- 打印一个文件的开始 N 行 ➜ `head -n [number-of-lines] [name-of-the-file]`
- 按页，翻阅文件 ➜ `less [name-of-the-file]`




