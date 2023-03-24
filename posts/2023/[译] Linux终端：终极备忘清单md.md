翻译自：[Linux Terminal: The Ultimate Cheat Sheet - DEV Community](https://dev.to/mauro_codes/linux-terminal-the-ultimate-cheat-sheet-2g5b)



如果你是一名 Linux 用户，终端可能是你所拥有的最给力的工具。但关于终端的使用，如果你想从中获益更多，你需要学习一些关于如何使用它的知识。



在过去的几个月里，我一直在频繁的使用终端，因此，我列出了一长串我经常使用的，我认为有用命令。如果我错过了哪些重要的东西，请告诉我，这样我就可以把它补充到后面新日志中。



💡 如果你认为本文有价值，你可以关注我：[Twitter](https://twitter.com/mauro_codes) 和 [Instagram](https://www.instagram.com/mauro.codes/)。



# TL;DR

**基本命令**

- 缩小 ➜ `[CTRL] + [+]`
- 放大 ➜ `[CTRL] + [-]`
- 打印当前目录 ➜ `pwd`
- 清除当前终端 ➜ `[CTRL] + [l]` or `clear`
- 给命令赋值别名 ➜ `alias [alias-name]="[command-to-run]"`
- 从文件中加载环境变量 ➜ `source [name-of-the-file-to-read-and-execute]`



**改变目录命令 (cd)**

- 移动到指定目录 ➜ `cd [name-of-your-directory]`
- 移动到父目录 ➜ `cd ..`
- 移动到用户家目录 ➜ `cd` or `cd ~`
- 移动到刚才访问的目录 ➜ `cd -`



**列表目录 (ls)**

- 列出全部可见文件和目录➜ `ls`

- 列出全部文件和目录（包括隐藏属性的文件）➜ `ls -a`

- 长清单格式 ➜ `ls -l`

- 对人类可读的格式 ➜ `ls -lh`

- 参数合并在一起，包括人类可读，以及显示隐藏文件 ➜ `ls -lah`

- 学习如何使用 ls 命令 ➜ `man ls`



**搜索**

- 查找二进制文件 ➜ `which [name-of-the-program]`
- 查找二进制文件，源文件和用户手册 ➜ `whereis [name-of-the-program]`
- 通过提供的名字，查找文件和目录 ➜ `find [path-to-search] -iname [name-of-the-file-you-want-to-search]`
  - 学习如何使用该命令 ➜ `man find`
- 获得一个命令的简要描述 ➜ `whatis [command-name]`



**历史**

- 获取上一条命令 ➜ 使用 `向上键` ⬆️ 来导航历史
- 获取之前的命令历史记录 (完整列表) ➜ `history`。
- 重复历史记录中的某条记录 ➜ `history` ➜ `![number-of-the-command-to-repeat]`
- 重复上一条命令（bang-bang command）➜ `!!`



**文件和目录操作**

- 创建新文件（不打开）➜ `touch [name-of-your-file]`

- 创建新文件，并使用编辑器打开 ➜ `vim [name-of-your-file]` or `nano [name-of-your-file]`

- 复制文件 ➜ `cp [source-path-of-your-file] [destination-path-for-your-file]`

- 创建新的目录 ➜ `mkdir [new-directory-name]`

- 删除空的目录 ➜ `rmdir [name-of-the-directory-you-want-to-remove]`










