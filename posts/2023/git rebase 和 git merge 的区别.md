`git rebase` 和 `git merge` 是 `git` 中两种不同方式的代码合并指令。

它们都用于将项目中的一个分支代码，合并到另一个分支上去。但两者的使用场景和实现方式不一样。主要区别有两点：

- 执行 git merge 时，git 查找要合并的两个分支之间所需的差异，并将这些差异合并到新提交中，以便新提交包含来自所有上游分支的更改。因此，merge 会完整保留每个提交的信息时间和历史。

- 当执行 git rebase 命令时，会创建一个临时分支，将需要移动的提交复制到临时分支上，并以提交顺序依次应用到新的基本分支上，因此，rebase 会使记录变得线性，然而时间戳和 hash id 会被改变。



## 构建测试库



当前的库有两个分支：

- main

- dev

当前提交记录

main：

- a

- b

- c

然后切到 dev 分支，进行两次提交

- dev_a

- dev_b

此时切回 main，再进行一次提交

- d

如图所示，当前的分支结构：

<img src="file:///C:/Users/shiweifu/AppData/Roaming/marktext/images/2023-03-12-14-33-15-image.png" title="" alt="" width="242">

我们想要将 dev 分支的代码，合并到 main 中。

## git merge 的处理方式

切换回 `main` 分支：

> git checkout main

然后执行：

> git merge dev

此时的提交记录：

<img src="file:///C:/Users/shiweifu/AppData/Roaming/marktext/images/2023-03-12-14-59-23-image.png" title="" alt="" width="209">



可以在记录中看到 dev 分支的代码提交记录，当前的 HEAD，来自 main 和 dev 两个分支。记录很完整。

## git rebase 的处理方式



再构建一个干净的库，切换到 `main` 分支：

> git checkout main



然后执行 rebase：

> git rebase dev



此时的提交记录：



<img src="file:///C:/Users/shiweifu/AppData/Roaming/marktext/images/2023-03-12-15-12-54-image.png" title="" alt="" width="196">



可以看到，使用 git rebase 合并代码的提交记录是线性的。



正常情况下，rebase 都是 main 这种长期分支，rebase dev 这种短期支线分支的，但也可以反向执行。



假设我们在开发 dev 分支时候，同事修复了 main 分支里的一个问题，提交了代码，此时我们在 dev 分支上，执行：

> git rebase main



这样可以将 `main` 分支的最新代码拉取过来，而不会产生  merge。



## 总结



`git merge` 是最常用的合并代码命令，也适用于大多数场景和正常的逻辑。但当团队规模较小或者不需要那么完整的提交记录时，可以试试 `git rebase`。两者结合也是不错的实践。
