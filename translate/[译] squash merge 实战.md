翻译自：[Two years of squash merge - DNSimple Blog](https://blog.dnsimple.com/2019/01/two-years-of-squash-merge/)



在 DNSimple，在日常的工作流中，我们每天都会用到 pull request。来 propose，review 和提交对 git repository 的更改。对大多数核心 repository，如 DNSimple web 应用，或者我们的基于厨师的基础架构，我们的策略是不提交到主分支，而是将更改保存到独立分支，并发起一个 pull request，然后由其他人进行 review（根据更改），审查通过后，在上线前，合并到主分支上。



两年多的两年前，我们决定改变开发团队的工作流程，始终使用git -squash合并。 在这篇文章中，我将突出这一决定的原因，如何为我们工作以及哪些好处。



### git merge: fast-forward, recursive, 和 squash

