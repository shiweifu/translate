翻译自：[Why you should check-in your node dependencies - Jack Franklin](https://www.jackfranklin.co.uk/blog/check-in-your-node-dependencies/)



在我担任现在这个职位前，在所有公司的任意团队，都建议不要将 `node_modules` 放到你的版本控制系统中去（本文以 git 为例）。有多种理由，都证明这似乎是坚实的建议：



- `node_modules` 目录中的代码，并不是团队所编写和维护。
- `node_modules` 目录的体积非常大，这会导致 `git diffs` 和 `pull request` 中有大量的无用信息。
- `node_modules` 目录中的代码，可以容易的使用 node 包管理器进行拉取。



我当前在 `Google` 的 ChromeDevTools 团队工作，我们把 `node_modules` 纳入版本控制系统。首先，我认为这不是一个寻常的做法，但我相信这么做一定有一些重大好处。我认为应该有更多的人考虑这种做法。