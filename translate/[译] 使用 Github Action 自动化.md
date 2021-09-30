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

