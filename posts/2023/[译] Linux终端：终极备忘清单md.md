翻译自：[Linux Terminal: The Ultimate Cheat Sheet - DEV Community](https://dev.to/mauro_codes/linux-terminal-the-ultimate-cheat-sheet-2g5b)

如果你是一名 Linux 用户，终端可能是你所拥有的最给力的工具。但关于终端的使用，如果你想从中获益更多，你需要学习一些关于如何使用它的知识。

在过去的几个月里，我一直在频繁的使用终端，因此，我列出了一长串我经常使用的，我认为有用命令。如果我错过了哪些重要的东西，请告诉我，这样我就可以把它补充到后面新日志中。

💡 如果你认为本文有价值，你可以关注我：[Twitter](https://twitter.com/mauro_codes)  和  [Instagram](https://www.instagram.com/mauro.codes/)。

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

- 列出全部可见文件和目录 ➜ `ls`

- 列出全部文件和目录（包括隐藏属性的文件）➜ `ls -a`

- 长清单格式  ➜ `ls -l`

- 对人类可读的格式  ➜ `ls -lh`

- 参数合并在一起，包括人类可读，以及显示隐藏文件 ➜ `ls -lah`

- 学习如何使用 ls 命令 ➜ `man ls`

**搜索**

- 查找二进制文件 ➜ `which [name-of-the-program]`
- 查找二进制文件，源文件和用户手册 ➜ `whereis [name-of-the-program]`
- 通过提供的名字，查找文件和目录 ➜ `find [path-to-search] -iname [name-of-the-file-you-want-to-search]`
  - 学习如何使用该命令 ➜ `man find`
- 获得一个命令的简要描述 ➜ `whatis [command-name]`

**历史**

- 获取上一条命令 ➜ 使用  `向上键` ⬆️ 来导航历史
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

输入  `clear`  或者  `[CTRL] + [l]`  清空整个终端屏幕，并得到一个干净的终端，以继续工作。

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

> 请注意，此别名将不会持续到以后的用途。如果要坚持别名，请在主目录中的.bashrc 文件的末尾添加它们。

## 加载一个文件

你可以使用 `source` 命令，逐行读取并执行文件中的内容。键入 `source [name-of-the-file-to-read-and-execute]`：

```
## Print the content of the script.txt file (contains two commands)
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ cat script.txt
echo "hello world" ## Print a hello message
cal                ## Print a calendar

## Source the script.txt to run each command inside
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ source script.txt
hello world

    January 2021
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29 30
31
```

## 修改目录命令（cd）

### 移动到指定目录

输入 `cd [name-of-your-directory]`：

```
## Check current directory
mauro_codes@DESKTOP-HIQ7662:~$ pwd
/home/mauro_codes

## Change directory
mauro_codes@DESKTOP-HIQ7662:~$ cd projects/

## Check new working directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ pwd
/home/mauro_codes/projects
```

### 移动到上级目录

键入 `cd ..`：

```
## Check current directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ pwd
/home/mauro_codes/projects

## Move to the parent directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ cd ..

## Check new working directory
mauro_codes@DESKTOP-HIQ7662:~$ pwd
/home/mauro_codes
```

### 移动到家目录

键入 `cd ~` 或者只是键入 `cd`，作为别名：

```
## Check current directory
mauro_codes@DESKTOP-HIQ7662:~/projects/awesome-app$ pwd
/home/mauro_codes/projects/awesome-app

## Move to the home directory
mauro_codes@DESKTOP-HIQ7662:~/projects/awesome-app$ cd ~

## Check new working directory
mauro_codes@DESKTOP-HIQ7662:~$ pwd
/home/mauro_codes
```

### 移动到你最后一次访问的目录

键入 `cd -`，导航到上一次访问的目录

```
## Check the current directory
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ pwd
/home/mauro_codes/projects/landing-page

## Move to another directory
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ cd /home/mauro_codes/

## Check the new directory
mauro_codes@DESKTOP-HIQ7662:~$ pwd
/home/mauro_codes

## Go back to the previus directory you were in
mauro_codes@DESKTOP-HIQ7662:~$ cd -
/home/mauro_codes/projects/landing-page
```

## 列表命令（ls）

列出当前所在目录的内容。

**列出所有可见的文件和目录**

键入ls（不带任何附加参数）以获取所有文件和目录（此命令将排除像点文件这样的隐藏文件）。

```
## Check the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ pwd
/home/mauro_codes/projects

## List the content for the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ ls
awesome-app  landing-page  nextjs-tailwindcss-blog-starter  personal-blog
```

## 列出全部文件和目录

键入`ls -a`以获取所有文件和目录（包括隐藏文件）。

```
## Check the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ pwd
/home/mauro_codes/projects

## List the content for the working directory (including hidden files)
mauro_codes@DESKTOP-HIQ7662:~/projects$ ls -a
.  ..  .config  .configu  awesome-app  landing-page  nextjs-tailwindcss-blog-starter  personal-blog
```

## 长列表格式

键入`ls -l`以获取所有可见的文件和目录，包括其他元数据，如权限、所有者、大小以及修改的日期和时间。

```
## Check the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects/nextjs-tailwindcss-blog-starter$ pwd
/home/mauro_codes/projects/nextjs-tailwindcss-blog-starter

## List the content for the working directory (using the long listed format)
mauro_codes@DESKTOP-HIQ7662:~/projects/nextjs-tailwindcss-blog-starter$ ls -l
total 140
-rw-r--r-- 1 mauro_codes mauro_codes   4487 Jan 22 12:55 README.md
drwxr-xr-x 1 mauro_codes mauro_codes    512 Jan 22 12:55 components
-rw-r--r-- 1 mauro_codes mauro_codes   1068 Jan 22 12:55 config.ts
drwxr-xr-x 1 mauro_codes mauro_codes    512 Jan 22 12:55 helpers
```

## 人类可读的格式

键入`ls -lh`以获取所有可见的文件和目录，这些文件和目录的格式为长列表格式，但具有可读格式（用户友好的文件大小）。



```shell
## Check the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects/nextjs-tailwindcss-blog-starter$ pwd
/home/mauro_codes/projects/nextjs-tailwindcss-blog-starter

## List the content for the working directory (using the long listed format + human readable format)
mauro_codes@DESKTOP-HIQ7662:~/projects/nextjs-tailwindcss-blog-starter$ ls -lh
total 140K
-rw-r--r-- 1 mauro_codes mauro_codes 4.4K Jan 22 12:55 README.md
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 components
-rw-r--r-- 1 mauro_codes mauro_codes 1.1K Jan 22 12:55 config.ts
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 helpers
```



## 合并参数



输入 `ls -lah`，获取全部的文件和目录（包括隐藏文件），以人类可读的格式输出。



```
## Check the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects/nextjs-tailwindcss-blog-starter$ pwd
/home/mauro_codes/projects/nextjs-tailwindcss-blog-starter

## List the content for the working directory (include hidden files + human readable format)
mauro_codes@DESKTOP-HIQ7662:~/projects/nextjs-tailwindcss-blog-starter$ ls -lah
total 140K
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 13:08 .
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 ..
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 .git
-rw-r--r-- 1 mauro_codes mauro_codes  362 Jan 22 12:55 .gitignore
-rw-r--r-- 1 mauro_codes mauro_codes 4.4K Jan 22 12:55 README.md
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 components
-rw-r--r-- 1 mauro_codes mauro_codes 1.1K Jan 22 12:55 config.ts
drwxr-xr-x 1 mauro_codes mauro_codes  512 Jan 22 12:55 helpers
```



**学习更多关于 `ls` 命令的内容**



您可以 `ls` 命令后面，一起使用一大堆可选参数。如果您想继续了解相关内容，在您的终端中键入 `man ls`，以显查看 `ls` 命令的用户手册。



## 搜索



**定位二进制文件**



如果要定位特定命令或程序的二进制文件（可执行文件）所在的位置。您可以使用`which`命令：

```
## Locate binary for the ls command
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ which ls
/usr/bin/ls
## Locate binary for git
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ which git
/usr/bin/git
```



**定位二进制文件，源码和你程序的用户手册**



您可以使用`whereis`命令来查找程序的二进制文件、源代码和用户手册。您可以使用`-b`、`-m`和`-s`参数将结果分别限制为二进制文件、手动文件和源文件。



```
## Locate binary, manual, and source for git
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ whereis git
git: /usr/bin/git /mnt/c/Program Files/Git/cmd/git.exe /usr/share/man/man1/git.1.gz
## Locate only binary and manual for Git, and only the manual for ls command
mauro_codes@DESKTOP-HIQ7662:~/projects/landing-page$ whereis -bm git -m ls
git: /usr/bin/git /mnt/c/Program Files/Git/cmd/git.exe /usr/share/man/man1/git.1.gz
ls: /usr/share/man/man1/ls.1.gz
```



**通过名称，定位文件和目录**



键入`find (path to search) -iname`〔要搜索的文件的名称〕，查找标题中包含给定名称的任何文件或目录。



- 要搜索的路径是可选的。如果未指定，find命令将在当前工作目录（及其子目录）上运行

- `-iname` 意味着我们的搜索将不区分大小写。

```
- If you want to learn more about this command, type `man find` to display the user manual.
## Check current working directory
mauro_codes@DESKTOP-HIQ7662:~/projects$ pwd
/home/mauro_codes/projects

## Find files that contain "posts" on my current working directory and its descendants
mauro_codes@DESKTOP-HIQ7662:~/projects$ find -iname posts
./nextjs-tailwindcss-blog-starter/pages/posts
./nextjs-tailwindcss-blog-starter/posts
## Find files that contain "posts" on a specific directory and its descendants
mauro_codes@DESKTOP-HIQ7662:~/projects$ find ./nextjs-tailwindcss-blog-starter/pages/ -iname posts
./nextjs-tailwindcss-blog-starter/pages/posts
```



**获取命令的简要描述**



如果您不知道某个命令的作用，请键入 `whatis [命令名]`，如下所示：



```
## Asking about the cat command
mauro_codes@DESKTOP-HIQ7662:~/projects$ whatis catcat (1)              - concatenate files and print on the standard output
## Asking about the find command
mauro_codes@DESKTOP-HIQ7662:~/projects$ whatis find
find (1)             - search for files in a directory hierarchy
```



## 历史



**获取上一条命令**



按向上箭头键可以访问最近的命令⬆️. 如果您想重复上一个命令，这将非常有用。假设我们移动到一个特定的目录，然后像这样检查我们的工作目录：

```
## Move to a specific directory
mauro_codes@DESKTOP-HIQ7662:~$ cd projects/awesome-app/

## Check the working directory
mauro_codes@DESKTOP-HIQ7662:~/projects/awesome-app$ pwd
/home/mauro_codes/projects/awesome-app
```

⬆️ 我们将获得 `pwd` 命令

⬆️⬆️ 我们将得到 `cd projects/awesome-app` 命令
