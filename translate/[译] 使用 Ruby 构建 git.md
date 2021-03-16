（翻译自：https://thoughtbot.com/blog/rebuilding-git-in-ruby）



Git 是一个版本控制系统，我们每天都用它管理我们的代码。它是给力的工具，但你是否有想过它是如何工作的？ Git 内部文档有点令人生畏，不完整，也没什么例子。自己研究 Git 的实现，也会让您感到头疼，特别是您不熟悉 C 语言的情况下。



将引擎拆解，再组装回去，是了解系统工作方式的最佳途径之一。这次我们不用编写 C 语言，而是使用我们 Rails 开发人员更熟悉的工具。让我们使用 Ruby 来实现一个 Git。



如果你想深入了解这个实现，可以在这里找到源码：https://github.com/JoelQ/rgit。



### Git 命令



Git 内置的模块遵循  [UNIX 哲学](https://en.wikipedia.org/wiki/Unix_philosophy)。每个模块都是以模块化的方式构建。每个命令都是其自己的脚本文件，而 `git` 命令只是他们的代理。`Git` 附带了许多内置命令，但只要遵循约定，就可以编写自定义命令。



```
#!/usr/bin/env ruby

# bin/rgit

command, *args = ARGV

if command.nil?
  $stderr.puts "Usage: rgit <command> [<args>]"
  exit 1
end

path_to_command = File.expand_path("../rgit-#{command}", __FILE__)
if !File.exist? path_to_command
  $stderr.puts "No such command"
  exit 1
end

exec path_to_command, *args
```



当我们调用脚本时，分为三种情况：



- 如果没有跟随子命令，会输出信息
- 如果子命令没有找到，输出错误信息
- 如果找到子命令，执行之



我们附带的额外参数，都会传递给子命令。



作为 UNIX 公民，当错误发生时，我们向标准错误信息流，输出错误信息，并返回非零的值。



### 初始化资料库



Git 在 `.git` 目录，存储项目的元信息。`git init` 命令初始化 `.git` 目录，以及一些相关的子目录：



```
.git
├── HEAD
├── config
├── objects
│  ├── info
│  └── pack
└── refs
    ├── heads
    └── tags
```



`HEAD` 是一个永远指向 `ref:refs/heads/master` 的文件。我们稍后会用到这个文件。`config` 包含关于 `repo` 的配置信息。为了简化，我们先忽略它。剩余项都是空目录。



生成这个结构，主要是用到 `Dir.mkdir。`



```
#!/usr/bin/env ruby

# bin/rgit-init

RGIT_DIRECTORY=".rgit".freeze
OBJECTS_DIRECTORY = "#{RGIT_DIRECTORY}/objects".freeze
REFS_DIRECTORY = "#{RGIT_DIRECTORY}/refs".freeze

if Dir.exists? RGIT_DIRECTORY
  $stderr.puts "Existing RGit project"
  exit 1
end

def build_objects_directory
  Dir.mkdir OBJECTS_DIRECTORY
  Dir.mkdir "#{OBJECTS_DIRECTORY}/info"
  Dir.mkdir "#{OBJECTS_DIRECTORY}/pack"
end

def build_refs_directory
  Dir.mkdir REFS_DIRECTORY
  Dir.mkdir "#{REFS_DIRECTORY}/heads"
  Dir.mkdir "#{REFS_DIRECTORY}/tags"
end

def initialize_head
  File.open("#{RGIT_DIRECTORY}/HEAD", "w") do |file|
    file.puts "ref: refs/heads/master"
  end
end

Dir.mkdir RGIT_DIRECTORY
build_objects_directory
build_refs_directory
initialize_head

$stdout.puts "RGit initialized in #{RGIT_DIRECTORY}"
```



这个脚本被称为 `rgit-init`，简称为 `rgit`。如果当前目录已经存在 `.rgit` 文件夹，我们输出错误信息，以及返回一个非 0 的码以标识出现异常。真实环境中的 `git`，允许你重新初始化当前库，但是这个功能超出我们 MVP 产品的范围了。



`init` 命令所做的事情有些冗长，但十分乏味。它创建了一堆目录以及头文件。



### 向存储区域添加文件



git 允许使用 `git add` 命令，将当前的文件状态，捕获为一个快照。这些快照的集合，被称为 `staging area`。快照列表和原信息，被存储在 `.rgit/index` 文件中。存储文件，需要以下几个步骤：



- 根据文件内容，创建 SHA 文件
- 压缩文件，创建二进制文件，并准备保存
- 将二进制文件，保存到 `rgit/objects/<first-two-characters-of-sha>/<rest of sha>` 路径下
- 添加 SHA 和原始文件的路径到索引中，我们稍后会读取它



索引二进制文件的 [格式说明](https://github.com/git/git/blob/master/Documentation/technical/index-format.txt)：



```
DIRC <version_number> <number of entries>

<ctime> <mtime> <dev> <ino> <mode> <uid> <gid> <SHA> <flags> <path>
<ctime> <mtime> <dev> <ino> <mode> <uid> <gid> <SHA> <flags> <path>
<ctime> <mtime> <dev> <ino> <mode> <uid> <gid> <SHA> <flags> <path>

# more entries
```



许多元信息，通过其他命令进行计算。如果你尝试打开这个文件，你将看到一堆乱码。



```
cat .git/index
```



```
bin/rgit-initTREE52 1?Ibin/rgitU?U?2????        ???
C??B=????''9bin2 0
?Cԣ̏k?i??`V:??3'9Z?6??赠xa?cǢbF
```



索引文件处于性能原因的考虑，所以保存为二进制文件，这就是为什么直接打开显示的是乱码。







