翻译自：[How to Test Rails Models with RSpec - Semaphore (semaphoreci.com)](https://semaphoreci.com/community/tutorials/how-to-test-rails-models-with-rspec)



测试是我们开发者，在开发过程中，消耗最多时间的部分。[好的测试](https://semaphoreci.com/blog/automated-testing-cicd)，会创造出高质量的软件，减少 bug；长期来看，会使我们的工作更加轻松。



在本文中，我们讨论 Ruby on Rails 中，几个关于测试的基本概念：



- 什么是 BDD
- 如何测试 Rails 中的模型
- 如何通过 Rails 测试业务逻辑
- 如何使用[持续集成](https://semaphoreci.com/continuous-integration)来自动化测试



### 什么是行为驱动开发？



行为驱动开发（behavior -driven Development，BDD）由多个子技术组成。在其内部，它将测试驱动开发（测试驱动开发允许短反馈循环）、领域驱动设计以及面向对象的设计和分析相结合。一个开发流程，它允许涉众和开发人员享受短反馈循环的优点，通过编写适量的代码和设计，使软件可以工作。你可以[在此](https://semaphoreci.com/community/tutorials/behavior-driven-development).，读到更多有关 BDD 的介绍