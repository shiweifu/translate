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



**删除命令 (rm)**

- 删除一个文件 ➜ `rm [name-of-your-file]`
- 删除一个目录，递归删除其中所有文件（不进行确认） ➜ `rm -rf [name-of-your-directory]`



**内容输出命令 (cat)**

- 显示一个文件内容 ➜ `cat [name-of-your-file]`
- 显示一个文件内容，并打印行号 ➜ `cat -n [name-of-your-file]`
- 复制一个文件内容到另一个文件 ➜ `cat [filename-whose-contents-is-to-be-copied] > [destination-filename]`
- 学习关于一个命令的使用 ➜ `man cat`



**移动命令 (mv)**

- 移动一个文件 ➜ `mv [source-path-of-your-file] [destination-path-for-your-file]`
- 重命名一个文件 ➜ `mv [name-of-your-file] [new name-of-your-file]`



# 基本命令



## 放大

输入 `[CTRL] + [+]`



## 缩小

输入 `[CTRL] + [-]`



## pwd: 打印当前工作目录命令

从根目录开始，打印工作目录的路径。



```
mauro_codes@DESKTOP-HIQ7662:~$ pwd
/home/mauro_codes

mauro_codes@DESKTOP-HIQ7662:~/projects$ pwd
/home/mauro_codes/projects

```



## 清理命令



输入 `clear` 或者 `[CTRL] + [l]` 清空整个终端屏幕，并得到一个干净的终端，以继续工作。



## 别名命令



如果你经常输入一长串命令，希望节省时间，你可以以一个较短的别名，来替代这个长命令的键入。输入 `alias [alias-name]="[command-to-run]"`，来分配新的别名：



```
## Running the ls command
mauro_codes@DESKTOP-HIQ7662:~$ ls
projects

## Assign an alias, so we don't need to add the arguments every time we need to list something
mauro_codes@DESKTOP-HIQ7662:~$ alias ls="ls -lah"

## Running ls again (we get the result of `ls -lah`)
mauro_codes@DESKTOP-HIQ7662:~$ ls
total 16K
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 17:41 .
drwxr-xr-x 1 root        root         512 Jan 22 10:38 ..
-rw------- 1 mauro_codes mauro_codes 3.0K Jan 22 23:58 .bash_history
-rw-r--r-- 1 mauro_codes mauro_codes  220 Jan 22 10:38 .bash_logout
-rw-r--r-- 1 mauro_codes mauro_codes 3.7K Jan 22 17:32 .bashrc
-rw-r--r-- 1 mauro_codes mauro_codes  807 Jan 22 10:38 .profile
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 projects
```


























































