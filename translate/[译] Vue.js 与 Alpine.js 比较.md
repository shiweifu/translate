Vue.js 是当前世界上最流行的 JavaScript 框架之一，发布于 2014 年，第三个版本即将发布，不会有大的变化。



那么我们为什么需要另一个 JavaScript 框架呢？为什么是 Alpine？与大多数现代 JavaScript 框架不同，使用 Alpine，并不需要编译，只需要简单引用，即可执行，全部特性都可以使用。它超级轻量级，在本文编写时候，Alpine 只有 4.3kb（v.1.9.3）。但是，对我而言，Alpine.js 最吸引我的地方是它的语法，如果你已经熟知 Vue，那你基本上了解了 Alpine，这使其非常适合 Vue 开发人员转换过来，而无需学习头疼的语法和某些奇怪的知识。Alpine 的作者 `Caleb Porzio`（Laravel Livewire 的作者）确保大部分的语法与 Vue 保持一致，例如：`v-for` 变成了 `x-for`，`v-show`变成了 `x-show` 等等等等，它也引进了一些缩写语法，如`x-on`，所以 `x-on:click=""`可以简写为 `@click=""`，你可以在 https://github.com/alpinejs/alpine#learn 了解到全部13 个语法。