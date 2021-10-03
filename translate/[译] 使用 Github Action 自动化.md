翻译自：[Automating a software company with GitHub Actions - PostHog](https://posthog.com/blog/automating-a-software-company-with-github-actions)



> 本文向你展示了一个好的 CI 方案，是如何自动化你的工作流，并帮助你专注于实际的挑战，而不是琐事。PostHog 为你的产品工作流做相同的事：我们是一个自托管的产品分析平台，可以帮助你了解用户是如何使用你的产品，以及如何改善他们的使用。**[立刻免费试用PostHog](https://posthog.com/?utm_medium=blog&utm_campaign=github-actions-post)**



当开发软件时，有很多不可避免的工作：构建新功能、修复bug，维护基础架构，启动新系统、逐步淘汰已弃用的解决方案、确保安全性、跟踪依赖关系……那是我们在考虑产品、人员或者操作之前。



上述的部分工作，需要的试用人脑 - 软件都是 1 和 0，但最终服务于人类的目的。如果没有人工智能的突破，发现人类的需求，并通过编程的方式，编译并满足之仍然是一个白日梦。



但是那些繁琐的任务呢？运行测试，发布版本，部署服务，保持存储库整洁 -- 那些无聊且每次都遵循相同模式的事情。尽管如此，它们仍然很重要。



我们不需要为这些任务提供人工智能（人工或其他方式）。我们只需要一次定义要完成的工作，并让这些任务，基于某些触发器运行。实际上，让我们更进一步：任何编程语言、您需要的任何支持服务、现成的解决方案，以及与版本控制系统的深度集成。



### Actions 101



此时，Github Actions 应运而生。通过 Actions，你可以定义每个仓库的工作流，它们鲁棒的运行在虚拟机中。它们每次执行一个不同类型的事件，这些事件包括，代码推送，拉取请求，给 issue 添加标签，或者自定义的工作流。



一个工作流包含任意数量的工作，每项工作有一系列的步骤，运行一个 shell 脚本，或者是一个独立的操作。而独立的行为，可以使用 TypeScript 进行编写，或者通过 Docker 容器实现无限的灵活度，可以在 Github Marketplace 找到大量的相关工具。



听起来已经十分给力了。接下来，让我们看看实践中的表现，以及一些已经在 PostHog Github 中，立即可用的例子。



请注意，类似的事情也有其他竞品的解决方案来实现。例如 GitLab CI/CD，甚至是 Jenkins。尽管 GitHub Actions 是一个较新的解决方案，但它有一个非常强大的生态系统，而且在 PostHog，我们从早期的 APRANET 时代，我们就是 GitHub 的狂热用户。



### 单元测试



单元测试对于确保软件的可靠性，至关重要 - 不要跳过编写它们，也不要跳过执行它们。最好的方法，是在每个正确工作的的 PR 上，运行它们。这在过去，被称为”极限编程“，但在今天，它是作为持续集成的标准实践。



下面是一个标准的 django 工d作流，它检查数据库是否缺少迁移，然后再运行测试。



请注意，我们如何并行的通过是如何通过定义一个矩阵，使三个指定 Python 版本！通过这种方式，我们确保通过一行代码，支持一系列版本。



```
on:
    - pull_request

jobs:
    django-tests:
        runs-on: ubuntu-latest
        strategy:
            fail-fast: false
            matrix:
                python-version: ['3.7.8', '3.8.5', '3.9.0']
        steps:
            - name: Check out the repository
            ses: actions/checkout@v2

            - name: Set up Python
              uses: actions/setup-python@v2
              with:
                  python-version: ${{ matrix.python-version }}

            - name: Install pip dependencies
              run: |
                  python -m pip install -r requirements-dev.txt
                  python -m pip install -r requirements.txt

            - name: Check if a migration is missing
              run: python manage.py makemigrations --check --dry-run

            - name: Run unit tests
              run: python manage.py test
```



#### 端对端测试



让您的软件每个构建块，都包含单元测试是件好事，，但是您的用户，需要将这一切组合在一起才能工作 --- 这就是端对端测试的意义所在。



我们使用 `Cypress` 在我们的应用程序上，运行这些，虽然这并不完美，但对我们来说也是一个福音。以下是 Cypress CI 工作流程的主要内容：



```
on:
    - pull_request

jobs:
    e2e-tests:
        runs-on: ubuntu-latest
        steps:
          - name: Check out the repository
            uses: actions/checkout@v2

          - name: Setup Node.js
            uses: actions/setup-node@v2
            with:
                node-version: 14

          - name: Install dependencies
            run: yarn

          - name: Build and start application
            run: echo "This is where you boot your application for testing"

          - name: Run end-to-end Cypress tests
            uses: cypress-io/github-action@v2

          - name: Archive test screenshots
            if: failure()
            uses: actions/upload-artifact@v2
            with:
                name: screenshots
                path: cypress/screenshots
```



我跳过了特定于应用程序的设置步骤和服务，但这里有一些有趣的事情：



1. Cypress 的现成套件运行器操作 - cypress-io/github-action 使工作流程变得如此简单。 它巧妙地处理了包括测试并行化和与 Cypress Dashboard 集成在内的任务——比 shell 脚本要好得多。
2. GitHub Actions 有一个名为“artifacts”的功能。 它是 GitHub 提供的存储，用于临时存储作业运行产生的文件并允许下载这些文件。 在这种情况下，actions/upload-artifact 上传失败测试的截图供我们查看。



#### 分析和格式化



功能测试验证事情是否按预期工作。 拥有有效的代码固然很好，但拥有编写好的代码更重要，否则随着时间的推移，开发会变得越来越难。



为了确保我们不会在每个新功能中添加过于凌乱的功能，我们使用：



- 分析代码，保证我们的代码符合最佳实践，并且没有漏掉一些东西
- 格式化代码，保证代码的一致性，以及可读性



而测试，对每一个 PR 进行测试，确保都通过测试，保持 master 中的代码都是高质量的。



```
on:
    - pull_request

jobs:
    code-quality:
        runs-on: ubuntu-latest
        steps:
            - name: Check out the repository 
              uses: actions/checkout@v2

            - name: Set up Node
              uses: actions/setup-node@v1
              with:
                  node-version: 14

            - name: Install package.json dependencies with Yarn
              run: yarn

            - name: Check formatting with prettier
              run: yarn prettier .

            - name: Lint with ESLint
              run: yarn eslint .
```


有一件事，我们还未覆盖到，是在每个 PR 上运行作业在实践中给我们带来了什么。 这是两件事：



1. 此类作业将成为 PR 检查，并显示在 PR 页面上，以及它们的状态。

![Bump labels](https://d33wubrfki0l68.cloudfront.net/0912d66a282a0257ae6893585c8f999284cfc2c8/46349/static/pr-160fe812143b3ba462ffd79c206fc3c4.png)

2. 可以要求选择 PR 检查，在这种情况下，阻止合并，直到所有必需的检查变为绿色。



#### 检查过时的 PR



如果你的团队一直在增长，仓库会有有大量的 PR。特别是通过 issue 提交的合并请求，一些 PR 会被搁置一段时间 - 可能是因为其他工作导致阻塞、等待 Review、取消优先级，或者只是一个概念验证。无论如何，一个 PR 无人管理的时间越多，后面就会制造更多的混乱。



我们来构建一个最小化的例子，来演示这种情况：



```
name: 'Handle stale PRs'
on:
    schedule:
        - cron: '30 7 * * 1-5'

jobs:
    stale:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/stale@v4
              with:
                  only: pulls
                  stale-pr-message: "This PR hasn't seen activity in a week! Should it be merged, closed, or worked on further? If you want to keep it open, post a comment or remove the `stale` label – otherwise this will be closed in another week."
                  close-pr-message: 'This PR was closed due to 2 weeks of inactivity. Feel free to reopen it if still relevant.'
                  days-before-pr-stale: 7
                  days-before-pr-close: 7
                  stale-issue-label: stale
                  stale-pr-label: stale
```

