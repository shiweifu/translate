翻译自：[Two years of squash merge - DNSimple Blog](https://blog.dnsimple.com/2019/01/two-years-of-squash-merge/            )



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



请注意，`git merge --sequash` 准备合并，但实际上，这不是一次提交。您需要执行 `git commit` 来创建合并提交。`git` 已经准备好包含所有压缩过的提交的信息。



### 我们解决了什么问题？



我们决定尝试 `--squash merge`，是为了改善代码库历史提交记录的质量。



提交记录基本上是不可改变的。从技术上来说，但是有几个原因，让我们通常不想这么做。为了简单起见，我们可以认为，越久远的提交记录，修改起来的难度越大。



编写好的提交记录是十分重要的，因为他们是您 git 历史的组成支柱。给一个完美的代码提交下个定义有些困难，但在我的经验中，一个好的提交，至少满足以下要求：



1. 结合与单个逻辑更改相关的所有代码更改（它可能是一个特征，错误修复或更改更改的个人更改部分）

2. 提供了一个解释性的提交消息，帮助人们了解更改的意图

3. 如果您从历史上独立地选择这一提交，则自己是有道理的



要求一个应该是您的默认编码习惯。 提交应表示原子改变，您应该避免组合彼此无关的多个更改。 虽然这似乎是显而易见的，但我已经看到提交更改编译脚本并在应用中引入一个新功能。 让我们使用另一个更实际的例子：您正在修复一个错误，因此我们希望更改要提交的软件以及回归测试，而不是与彼此无关的不同提交。



第二点是一个著名的问题，有数百篇文章尝试定义一个好的提交信息应该是什么样的，并试图教程序员写一个良好的提交信息。 [官方 git 页](https://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project)有一些关于提交信息的指导方针：



```
Short (50 chars or less) summary of changes

More detailed explanatory text, if necessary.  Wrap it to
about 72 characters or so.  In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body.  The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.

Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Typically a hyphen or asterisk is used for the bullet,
    preceded by a single space, with blank lines in
    between, but conventions vary here
```



上面的指导方针，被 Tim Pope 扩写为一篇文章，这篇文章应该是我能记得的最早的一篇探讨这个主题的文章。



至此，我们已经看到，编写一个良好的提交记录信息，需要遵守一些原则。我们同样也看到，您可以遵循一些原则，以及使用一些工具来强行确保我们遵循这些原则。



![img](https://d33wubrfki0l68.cloudfront.net/7944ff013cfb31393050aa8acea62baf3cd4578c/4f47a/files/2018/squashmerge-commit-rules.png)



但是，写一个好的提交记录还是很难，因为这不仅是客观标准的问题。您可以编写格式完美，但完全无用的提交信息。



![img](https://d33wubrfki0l68.cloudfront.net/f4285c80e7293675690a8f1cba1025cdbc844259/a7e2d/files/2018/squashmerge-stupid-commit-message.png)



甚至可以更加没用一些：



![img](https://d33wubrfki0l68.cloudfront.net/0222c88381a22a3262143a2ec0fa7ebf1d9e9762/f2edd/files/2018/squashmerge-generic-commit-message.png)



如果你创建过诸如：修复测试，修复CI，修复某个问题，增加Bar 之类的提交记录，请举起你的手。



您可能会问，这种提交信息究竟哪里有问题呢？有三点。一个好的提交记录（好的提交信息），在数百条提交记录中，它有自己独特的意义（或者可以提供足够的描述信息，来说明这次提交为什么会发生）。



让我们做一个实验。你能告诉我这次的代码变更是为了什么吗？



![img](https://d33wubrfki0l68.cloudfront.net/36af50d324d2d372f98e5798289d626df764b1b7/71a05/files/2018/squashmerge-bad-commit-fix.png)



事实上，这次修改，调整了单元测试的测试目标，使其通过。但想象一下，如果有人在三年后，被 第 286 行代码所绊倒，那么这三年就会使人困惑。提交记录和代码，都没有解释为什么测试目标会被打破，何时打破了他们，以及为什么第 286 行代码的变更是必须的。孤立来看，这次提交记录毫无意义。



另一个常见的无用提交记录的例子，是下面历史记录中的第一条：



![img](https://d33wubrfki0l68.cloudfront.net/4fabd4ee2ac1d4757097799dadb26fad587bcd61/7bdc8/files/2018/squashmerge-bad-commit-dependencies.png)



想象一下，您整通过数百个提交记录中，来检查某种错误发生的时间以及原因。我认为你会同意第一次的提交在审查中，需要注意的程度要高于第三次。从提交信息的角度来看，它需要您（至少）打开提交详情以检查更改。









