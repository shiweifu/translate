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

## 全局正则表达式搜索（grep命令）

Linux `grep` 命令是一个字符串和模式匹配实用程序，用于显示来自多个文件的匹配行。

**搜索文件中的一个字符串**

键入 `grep [term-to-search] [source-file-to-search]` 来搜索文件中的某个文本。如果您想打印每个匹配的行号，您可以选择添加 `-n` 参数。

```
## List the content for the working directory
mauro_codes@mauro-desktop:~/projects/landing-page$ ls
README.md  index.html  main.js  script.txt

## Print the content of the index.html file
mauro_codes@mauro-desktop:~/projects/landing-page$ cat index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="ie=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <title>My Website</title>
  </head>

  <body>
    <script src="js/main.js"></script>
  </body>
</html>

## Search for "meta" within the index.html file using grep
mauro_codes@mauro-desktop:~/projects/landing-page$ grep meta index.html -n
4:    <meta charset="utf-8" />
5:    <meta http-equiv="x-ua-compatible" content="ie=edge" />
6:    <meta name="viewport" content="width=device-width, initial-scale=1" />
```

## 搜索一个文件，但不区分大小写

键入  `grep -i [term-to-search] [source-file-to-search]` ，来执行大小写无关的搜索：

```
## Print the content of the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ cat main.js
const sum = (num1, num2) => {
        return num1 + num2
}

// Call the Sum function
sum(10,4)

## Search for "sum" without ignoring case (we get 2 results)
mauro_codes@mauro-desktop:~/projects/landing-page$ grep sum main.js -n
1:const sum = (num1, num2) => {
6:sum(10,4)

## Search for "sum" using the ignore case argument (we get 3 results)
mauro_codes@mauro-desktop:~/projects/landing-page$ grep sum main.js -in
1:const sum = (num1, num2) => {
5:// Call the Sum function
6:sum(10,4)
```

## 搜索文件中，不匹配的行

键入  `grep -v [term-to-search] [source-file-to-search]` 来获取文件中，全部不匹配的行。

```
## Print the content of the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ cat main.js
const sum = (num1, num2) => {
        return num1 + num2
}

// Call the Sum function
sum(10,4)

## Search for each line that doesn't include "sum" (case sensitive)
mauro_codes@mauro-desktop:~/projects/landing-page$ grep sum main.js -v
        return num1 + num2
}

// Call the Sum function
```

## 递归搜索目录

键入 `grep -r [term-to-search] [path-to-directory-to-search]`  来搜索当前文件夹下，嵌套的目录和子目录中的内容。

```
## List the content for the working directory
mauro_codes@mauro-desktop:~/projects/landing-page$ ls
README.md  index.html  main.js  script.txt  temp

## Search recursively for "sum" within the current directory (including sub-directories)
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -r sum .
./index.html:    <p>Calling the sum function:<p>
./index.html:    <!-- TODO: Call the sum function and display the value -->
./main.js:const sum = (num1, num2) => {
./main.js:sum(10,4)
./temp/index.html:    <p>Calling the sum function:<p>
./temp/index.html:    <!-- TODO: Call the sum function and display the value -->
```

## 多次搜索一个文件

键入 `grep -E "[first-term-to-search|second-term-to-search]" [source-file-to-search]` 来搜索一个文件中的多个部分。

```
## Print the content of the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ cat main.js
const sum = (num1, num2) => {
        return num1 + num2
}

// Call the Sum function
sum(10,4)

const printMessage(message) {
        console.log(message)
}

// Call the printMessage function
printMessage("Hello world")

## Search for "sum" or "printMessage" within the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -E "sum|printMessage" main.js
const sum = (num1, num2) => {
sum(10,4)
const printMessage(message) {
// Call the printMessage function
printMessage("Hello world")
```

## 统计搜索结果

键入 `grep -c [term-to-search] [source-file-to-search]`，来计算当前文件，共有多少行的结果返回。

```
## List the content for the working directory
mauro_codes@mauro-desktop:~/projects/landing-page$ ls
README.md  index.html  main.js  script.txt  temp

## Count how many times "sum" appears on the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -c sum main.js
2

## Count how many times "sum" or "printMessage" appears on the main.js file 
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -c -E "sum|printMessage" main.js
5

## Recursive count to get how many times "sum" or "printMessage" appears in the current working directory
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -c -E -r "sum|printMessage" .
./index.html:2
./main.js:5
./README.md:0
./script.txt:0
./temp/index.html:2
```

## 查看匹配到的文件名

键入 `grep -l [term-to-search] [matching-files-to-search]`，获取包含搜索内容的文件列表。

```
## List the content for the working directory
mauro_codes@mauro-desktop:~/projects/landing-page$ ls
README.md  about.html  index.html  main.js  script.txt  temp

## Get a list of html files that contains "<h1>"
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -l "<h1>" *.html
index.html

## Get a list of html files that contains "<p>"
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -l "<p>" *.html
about.html
index.html

## Recursively get a list of files that contains "<p>" within the current directory
mauro_codes@mauro-desktop:~/projects/landing-page$ grep -l -r "<p>" .
./about.html
./index.html
./temp/index.html
```

## 管道

管道是Linux上最有用的命令行功能之一，允许您通过将多个操作”管道化“在一起来执行复杂的操作，这意味着管道中每个命令的输出将作为下一个命令的输入。

## 管道过滤搜索结果到一个新的文件

比方说，我想得到一份我所有博客文章的列表，标题中有“苗条”一词。我想在一个名为`svelte-articles.txt` 的新文件上写下完整的列表。

我们可以使用管道列出我的所有文章，使用grep过滤那些包含“svelte”的文章，并将过滤后的列表保存在一个新文件中。

```
## Check that my current working directory is the "posts" folder that includes all my posts
mauro_codes@mauro-desktop:~/projects/maurogarcia.dev/posts$ pwd
/home/mauro_codes/projects/maurogarcia.dev/posts

## Piping the list of posts with grep to check how many posts include the term "svelte" (I get five items)
mauro_codes@mauro-desktop:~/projects/maurogarcia.dev/posts$ ls | grep svelte
5

## Piping the list of post --> Filter svelte posts --> move the content to a new file using cat
mauro_codes@mauro-desktop:~/projects/maurogarcia.dev/posts$ ls | grep svelte | cat > /home/mauro_codes/projects/svelte-articles.txt

## Print the content of our new file
mauro_codes@mauro-desktop:~/projects/maurogarcia.dev/posts$ cat /home/mauro_codes/projects/svelte-articles.txt
-rw-r--r-- 1 mauro_codes mauro_codes 4.2K Jan 27 18:29 -5-things-i-love-about-svelte-.md
-rw-r--r-- 1 mauro_codes mauro_codes  11K Jan 27 18:29 angular-vs-svelte-card-component.md
-rw-r--r-- 1 mauro_codes mauro_codes 5.3K Jan 27 18:29 component-driven-development-with-svelte.md
-rw-r--r-- 1 mauro_codes mauro_codes 5.0K Jan 27 18:29 how-to-build-your-next-chrome-extension-with-svelte.md
-rw-r--r-- 1 mauro_codes mauro_codes 4.7K Jan 27 18:29 sapper-svelte-tailwindcss-boilerplate.md
```

## 搜索命令历史

通过键入 `history | grep“[term to search]”`，可以使用管道使用grep搜索命令历史记录。

感谢 [@bradnichol](https://dev.to/bradnichol) 在我之前的帖子中提出了这种方法！

```
## Searching for the term svelte on your command history
mauro_codes@mauro-desktop:~$ history | grep "svelte"
  415  ls | grep svelte
  416  ls | grep -C svelte
  417  ls | grep -c svelte
  418  ls | grep -c svelte |
  419  ls | grep -c svelte | cat  420  ls | grep svelte | cat  421  ls | grep svelte | cat > /home/mauro_codes/projects/svelte-articles.txt
  422  cat /home/mauro_codes/projects/svelte-articles.txt
  444  ls grep -c svelte
  445  ls | grep -c svelte
  446  cat /home/mauro_codes/projects/svelte-articles.txt
  448  history | grep "svelte"
```

## 权限：更改文件模式位命令（chmod）

在Linux中更改权限是一个广泛的主题，值得发表自己的文章。但我决定加入一些可以派上用场的例子。

您可以使用 `chmod` 命令向不同用户添加或删除一个或多个文件的读取、写入和执行权限。

作为参考，这里有一个例子，说明当我们使用 `ls-lah` 时，如何显示每个文件或目录的权限：

![Linux permissions](https://res.cloudinary.com/practicaldev/image/fetch/s--G1zf9R4v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fpk9bna.png)

## 向每个人添加执行权限

键入 `chmod a+x [文件名]` 或 `chmod+x [文件名]` 为特定文件的每个人添加执行权限：

```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------------- 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Add execute permission to everyone in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod +x main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------x--x--x 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```

## 删除所有人的执行权限

键入`chmod a-x [文件名]` 或 `chmod-x [文件名]` 以删除特定文件的所有人的执行权限：

```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxrwx 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Remove the execute permissions to everyone in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod a-x main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rw-rw-rw- 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```

## 向所有者添加执行权限

键入 `chmod u+x [name-of-the-file]` 来只向文件的所有者，添加可执行权限。

```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------------- 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Add the execute permissions only to the owner in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod u+x main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------x------ 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```

## 移除其他用户的可写入权限

键入 `chmod o-w [name-of-the-file]`，来移除指定文件的，其他用户的可写入权限（非拥有者或者文件所属组）。

```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxrwx 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Remove the write permission only to other users (not owner or group) in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod o-w main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxr-x 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```

## 为文件拥有者，增加可执行权限

键入 `chmod u+x [name-of-the-file]` 来为指定文件，增加可执行权限：

```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------------- 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Add the execute permissions only to the owner in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod u+x main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------x------ 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```

## 移除其他用户的写入权限

键入 `chmod o-w [name-of-the-file]` 来移除其他用户（非拥有者或者拥有群组），指定文件的写入权限：

```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxrwx 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Remove the write permission only to other users (not owner or group) in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod o-w main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxr-x 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```



## 为当前组添加读取权限

T

键入 `chmod g+r [name-of-the-file]` 来为指定文件添加目标文件的读取权限，针对当前组有效：



```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------------- 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Add the read permission only to the group in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod g+r main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
---------r----- 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------------- 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```



## 移除所有用户针对目标文件的读取和写入权限



键入 `chmod a-wr [name-of-the-file]` 来移除目标文件，针对所有用户的写入和读取权限



```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxrwx 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Remove the write and read permission to everyone in the main.js file
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod a-wr main.js

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------x--x--x 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```



## 删除每个人对多个文件的写入和读取权限



键入 `chmod a-wr *.*` 以删除对当前工作目录中所有文件的所有人的写入和读取权限。



```
## Check file permissions
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
-rwxrwxrwx 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
-rwxrwxrwx 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt

## Remove the write and read permissions to everyone for all the files in the current working directory
mauro_codes@mauro-desktop:~/projects/landing-page$ chmod a-wr *.*

## Check permissions again
mauro_codes@mauro-desktop:~/projects/landing-page$ ls -lah
total 0
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 17:49 .
drwxr-xr-x 1 mauro_codes mauro_codes 512 Jan 27 20:00 ..
--------x--x--x 1 mauro_codes mauro_codes 206 Jan 27 16:36 main.js
--------x--x--x 1 mauro_codes mauro_codes  23 Jan 24 17:31 script.txt
```



## 组



## 列出所有可用的组



键入`getent group`以获取一个列表，其中包括系统中所有可用的组。



```
mauro_codes@mauro-desktop:~/projects/landing-page$ getent group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
mauro_codes:x:1000:
```



## 列出我的帐户分配到的所有组



键入 `groups` 以获取您所属的所有组的列表。



```
## List all my groups
mauro_codes@mauro-desktop:~/projects/landing-page$ groups
mauro_codes adm dialout cdrom floppy sudo audio dip video plugdev netdev
```



## 搜索特定组（使用管道）



键入 `getent group | grep [要搜索的组名]`以使用管道搜索特定的组。



```
## List all the groups that contains the term "mauro_codes"
mauro_codes@mauro-desktop:~/projects/landing-page$ getent group | grep "mauro_codes"
adm:x:4:syslog,mauro_codes
mauro_codes:x:1000:

## List all the groups that contains the term "root"
mauro_codes@mauro-desktop:~/projects/landing-page$ getent group | grep "root"
root:x:0:
```




