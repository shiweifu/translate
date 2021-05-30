翻译自：[Two years of squash merge - DNSimple Blog](https://blog.dnsimple.com/2019/01/two-years-of-squash-merge/)



在 DNSimple，在日常的工作流中，我们每天都会用到 pull request。来 propose，review 和提交对 git repository 的更改。对大多数核心 repository，如 DNSimple web 应用，或者我们的基于厨师的基础架构，我们的策略是不提交到主分支，而是将更改保存到独立分支，并发起一个 pull request，然后由其他人进行 review（根据更改），审查通过后，在上线前，合并到主分支上。



两年多的两年前，我们决定改变开发团队的工作流程，始终使用git -squash合并。 在这篇文章中，我将突出这一决定的原因，如何为我们工作以及哪些好处。



### git merge: fast-forward, recursive, 和 squash



在我们讨论为什么要使用 `--squash` merge 之前，让我们来快速看一下，git 中常用的合并操作命令。



注意：本文没有完整的解释 `git merge` 命令。如果您想详细了解 `git merge`，可以在这里看到文档：[`git merge 文档`](https://git-scm.com/docs/git-merge)。



首先，`git merge` 设计的目的，是将其他分支合并到当前分支。简单来说个使用场景：比如有两个分支，`bugfix` 和 `master`，我们想将 `bugfix` 合并到 `master` 中。



### fast-forward



如果 `master` 没有从分支中分离，则在合并的时候， git 将简单的将指向当前分支的引用，移动到 `bugfix` 分支的最后一个提交。



```
        C - D - E           bugfix
      /
A - B                       master
```



`git merge` 之后：



```
A - B - C - D - E           master/bugfix
```



下面是合并的输出：



```
➜  merge-examples git:(master) git merge --ff bugfix
Updating 9db2ac7..3452cab
Fast-forward
```



这被称为 [*fast forward*](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)，快进。



### 不快进的情况下



默认行为下，Git 尽可能的使用快进的方式进行处理。但是我们也可以通过 git 配置或者传递 `--no-ff`（no fast-forward） 选项给 `git merge`，来改变着一行为。在默认情况下，如果 git 检测到该分支没有冲突，则创建一个 merge commit。



```
        C - D - E           bugfix
      /
A - B                       master
```



`git merge --no-ff` 执行后：



```
        C - D - E           bugfix
      /           \
A - B ------------ F        master
```



下面是 merge 的输出：

```
➜  merge-examples git:(master) git merge --no-ff bugfix
Already up to date!
Merge made by the 'recursive' strategy.
```



### 递归策略



到此，我们讨论的都是将 `bugfix` 合并 master 分支，但未遇到冲突的场景。然而，这过于乐观。哪怕是在一个小型的多人开发团队，也有可能出现同一时间提交的导致冲突的情况。下面看个例子：



```
        C - D - E           bugfix
      /
A - B - F - G               master
```



`F` 和 `G` 提交，导致 `master` 分支 与 `bugfix` 分支无法合并。因此，`git` 不能简单的快进到 `E`，否则将会丢失这两个提交。



在本例中，`git` 将（一般）采取递归合并的策略。其结果是合并提交，将两个历史记录合并到一起：



```
        C - D - E           bugfix
      /           \
A - B - F - G ----- H       master
```



下面是合并的输出：



```
➜  merge-examples git:(master) git merge --no-ff bugfix
Already up to date!
Merge made by the 'recursive' strategy.
```



### Sequash merge



与之前的合并不同，Sequash 合并分支的提交，将被压缩成一个，并应用于目标分支。下面是一个例子：

```
        C - D - E           bugfix
      /
A - B - F - G               master
```



在 `git merge --squash && git commit` 执行之后：

```
        C - D - E           bugfix
      /
A - B - F - G - CDE         master
```



`CDE` 是 `C + D + E` 三个分支的的变化合并而成的一个分支。`Squashing` 保留更改，但是丢弃了 `Bugfix` 的提交记录。





