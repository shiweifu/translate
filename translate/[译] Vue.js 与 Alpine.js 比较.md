Vue.js 是当前世界上最流行的 JavaScript 框架之一，发布于 2014 年，第三个版本即将发布，不会有大的变化。



那么我们为什么需要另一个 JavaScript 框架呢？为什么是 Alpine？与大多数现代 JavaScript 框架不同，使用 Alpine，并不需要编译，只需要简单引用，即可执行，全部特性都可以使用。它超级轻量级，在本文编写时候，Alpine 只有 4.3kb（v.1.9.3）。但是，对我而言，Alpine.js 最吸引我的地方是它的语法，如果你已经熟知 Vue，那你基本上了解了 Alpine，这使其非常适合 Vue 开发人员转换过来，而无需学习头疼的语法和某些奇怪的知识。Alpine 的作者 `Caleb Porzio`（Laravel Livewire 的作者）确保大部分的语法与 Vue 保持一致，例如：`v-for` 变成了 `x-for`，`v-show`变成了 `x-show` 等等等等，它也引进了一些缩写语法，如`x-on`，所以 `x-on:click=""`可以简写为 `@click=""`，你可以在 https://github.com/alpinejs/alpine#learn 了解到全部13 个语法。



（你可能好奇为什么 Alpine 使用 `x-` 而不是 `a-`，其实这是因为 Alpine 的名字确定之前，Alpine 被称为 `project-x`，这是对他过去名称的致敬。）



我们接下来看的简单的应用程序是一个简单的待办事项。“WWWWW HHHHHYYYYY？为什么还要再做一个例子？”我听到你在问……嗯，这个应用显示了很多基本概念，所以……



### 从 Vue 开始



最简单的方式使用 Vue，是从 CDN引入：



```
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
```



当然，Vue 提供了 Vue CLI，要使用 Vue CLI，你需要安装 node，你可以在此找到相关信息：https://nodejs.org，然后在终端，通过命令行进行安装：



```
npm install -g @vue/cli
```



在你的 Vue CLI 命令行安装完毕后，你可以创建你的项目：



```
vue create my-project
```



接下来进入你的项目目录：



```
cd my-project
npm run serve
```



### 开始使用 Alpine



Alpine 当前没有命令行工具，但使用 CDN 进行引入超级方便：新建一个 html 文件，将下面的标签贴进去即可。



```
<script src="https://cdn.jsdelivr.net/gh/alpinejs/alpine@v1.9.3/dist/alpine.js" defer></script>
```



### 创建我们的模板



我们打算创建一个 todo 应用，所以我们需要实现一些关键特性：



1. Todo 列表
2. 复选框，用于标记 Todo 任务已经完成
3. 删除按钮，删除任意 Todo
4. 表单，用于提交新的 Todo



###  创建 Vue 版



```
<template>
    <div id="app">
       <form @submit.prevent="addNewTask()">
           <input type="text" v-model="task" />
           <button type="submit">Add new task</button>
       </form>
       <ul>
           <li v-for="todo in todos" :key="todo.id" :class="{ 'is-complete': todo.isComplete === true }">
               <span v-text="todo.task"></span>
               <input type="checkbox" v-model="todo.isComplete" />
               <button @click="removeTask(todo.id)">Delete</button>
           </li>
       </ul>
    </div>
</template>
```



### 创建 Alpine 版



```
<div id="app" x-data="todos()">
    <form @submit.prevent="addNewTask()">
        <input type="text" x-model="task" />
        <button type="submit">Add new task</button>
    </form>
    <ul>
        <template x-for="todo in todos" :key="todo.id">
            <li :class="{ 'is-complete': todo.isComplete === true }">
                <span x-text="todo.task"></span>
                <input type="checkbox" x-model="todo.isComplete" />
                <button @click="removeTask(todo.id)">Delete</button>
            </li>
        </template>
    </ul>
</div>
```



Vue 版和 Alpine.js 版的实现代码十分相似，只有一些微小的区别。让我们来看看这些区别：



- 最明显的变化是对于 `<template> ` 标签的使用，template 标签是 HTML 5 中引入的一个元素，编写在其中的代码不会被显示。在 Vue 中，我们的代码包围在 `<template>` 标签，而在 Alpine.js 中，我们不需要这么做，它可以使用纯 HTML 标签。最后，由于 Alpine.js 中没有虚拟 DOM，例如 for 循环和 if 语句，这种智能功能的实现，我们只能通过包裹在 template 中来实现。
- 与 Vue 不同，在 Alpine 中，我们需要自己指定数据和元素的关系，我们可以看到使用 `x-data` 指令的 `#app` 父元素。在我们的例子中，我们绑定了 `todos()` 函数，在此函数中，保存我们所有的数据和方法。
- 其他的区别就是 `v-*` 和 `x-*` 写法的区别了。



我们的 Vue 数据：



```
export default {
    data() {
        return {
            increment: 3,
            task: '',
            todos: [
                {
                   id: 1,
                   task: 'Open VS code',
                   isComplete: true
                },
                {
                    id: 2,
                    task: 'Write a todo app in vuejs',
                    isComplete: false
                }
            ]
        }
    },
    /**/
}
```



我们的 Alpine 数据：



```
function todos() {
    return {
        //data
        increment: 3,
        task: '',
        todos: [
           {
               id: 1,
               task: 'Open VS code',
               isComplete: true
           },
           {
               id: 2,
               task: 'Write a todo app in alpinejs',
               isComplete: false
           }
       ],
    /**/
    }
}
```



像我们的模板一样，Vue 和 Alpine 的数据定义，两者的区别很小。`/**/` 标记所在的位置，插入上面模板的代码。两个例子均使用函数返回的`object`作为数据，在 Vue  中，我们使用：



```
data() {/**/}
```



在 Alpine 中，我们使用 `todos()` 函数，配合 `x-data` 指令，定义在模板中：



```
todos() {/**/}
```



还有两个小地方不一样：



- 在 Vue 中，我们写在 <script> 标签中的数据方法，需要导出，而在 Alpine 中，我们可以直接将包含数据的函数写在 `<script>` 中。

  

