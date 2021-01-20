翻译自：https://css-tricks.com/alpine-js-the-javascript-framework-thats-used-like-jquery-written-like-vue-and-inspired-by-tailwindcss/



我们已经有了许多非常流行的前段框架，比如 React，Vue，Angular，以及 Svelte。我们还需要另外的 JavaScript 库吗？我们来看看  [Alpine.js](https://github.com/alpinejs/alpine)，然后你自己来判断。Alpine.js 不用用来给开发者开发单页面应用的。他很轻量级（压缩后约等于7KB），设计用来通过标记语言，编写客户端的 JS 代码。



语法借鉴自 `Vue` 和 `Angular`。这意味着如果你使用过这两款框架，你会对语法感到熟悉。但再重复一下，Alpine.js 不是用来开发 SPA的，而是通过少量的 JavaScript 来增强模板。



举个例子，这是一个 Alpine.js 模板，用来实现 "alert" 组件。



```
<div class="m-4" x-data="{ msg: 'Something not ideal might be happening.', level: '', capitalize: str => str.charAt(0).toUpperCase() + str.slice(1) }">
  <label for="alert-message" class="block mb-2">Alert Message</label>
  <input id="alert-message" name="alert-message" class="block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500 mb-2" x-model="msg" />
  <button @click="level = 'info'" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
    Alert Info
  </button>
  <button @click="level = 'error'" class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded">
    Alert Error
  </button>
  <button @click="msg = '', level = ''" class="font-bold py-2 px-4 rounded">
    Clear
  </button>
  <template x-if="msg && level">
    <div role="alert" class="mt-2">
      <div class="text-white font-bold rounded-t px-4 py-2" :class="{'bg-red-500': level === 'error', 'bg-blue-500': level === 'info'}" x-text="capitalize(level)">
      </div>
      <div class="border border-t-0 rounded-b px-4 py-3" :class="{'bg-red-100 text-red-700 border-red-400': level === 'error', 'bg-blue-100 text-blue-700 border-blue-400': level === 'info'}">
        <p x-text="msg"></p>
      </div>
    </div>
  </template>
</div>
```



警告信息使用 `x-model="msg"`，以两种方式绑定。不通等级的的警告信息使用 `level` 属性进行设定。当 `msg` 和 `level` 两个属性都有值时，才进行显示。



### Alpine 像是一个以 declar饿ative 方式实现渲染的一个 jQuery 和 JavaScript 替代方案



Alpine.js 是一个类似 `Vue` 模板语法的，用于替代 jQuery 和 原生 JavaScript 的类 React/Vue/Svelte 等框架的方案。



Alpine.js 还很年轻，他的一些使用方式 jQuery 并不具备，让我们简单的比较一下两者。



#### 查询 vs 绑定



JavaScript 的体积和特性的背后是跨浏览器的兼容，这通常被称为 `jQuery Core`，通常利用他们可以查询 DOM 和操作他们。



Alpine.js 解决 jQuery Core 的问题，是通过 `x-bind` 绑定属性，通过声明式的方式来绑定 DOM。它可以用于将任何属性绑定到 Alpine.js 组件上。`Alpine.js` 与其他声明式的框架一样（React，Vue），暴露 `x-ref` 作为直接通过 JavaScript 操作 DOM 元素的方式，以解决在绑定无法满足需求时，直接操作 DOM 节点。



#### 事件处理



jQuery 同样提供一种方式来处理事件，创建并触发事件。`Alpine.js` 提供 `x-on 指令`和 `$event 魔法值`，允许 JavaScript 方法来处理事件。要触发自定义事件，`Alpine.js` 提供 `$dispatch magic property` 来实现，它封装了浏览器的 [事件和处理事件的接口](https://developer.mozilla.org/en-US/docs/Web/API/Event)。



#### 动效处理 



jQuery 有一个核心特性，就是它封装了许多方法，让用户可以方便的实现各种动画效果，如 `slideUp`，`slideDown`，`fadeIn`，`fadeOut` 等。Alpine.js 提供一组 `x-transition` 指令，通过添加和删除元素的类，来实现动画。灵感来自 Vue Transition API。



同样的，jQuery 的 Ajax 客户端调用，在Alpine.js 中也没有完全一样的解决方案，你可以使用 Fetch API 或者 axios 这类的第三方 HTTP 库。



#### 插件



jQuery 的插件系统也同样很有价值。而 Alpine.js 的生态系统中，还没有类似的实现。共享 Alpine.js 组件很简单，通常只需要复制、粘贴。Alpine.js 的组件只是函数，他们往往不访问 Alpine.js 本身，通过在脚本的不同页面上，包含<script>标签，使他们相对容易共享。当 Alpine 初始化后者传递给绑定时，它的一些魔法属性会被添加，如 `x-on` 中绑定 `$event`。



虽然存在一些关于扩展的讨论，并已经有了一些将其他库挂载链接到 Alpine.js 核心事件的请求，但当前还没有 Alpine.js 扩展的示例。Alpine.js 的作者 `Caleb Porzio` 似乎是基于 Vue API 的处理来作出决策的，我希望将来任何有关扩展的点，都受到 Vue.js 提供的内容启发。



#### 体积



Alpine.js 相较于 jQuery，更加轻量级。gzipped 压缩前为 21.9kB，压缩后为 7.1kB。而 jQuery 压缩前 为 87.6kB，压缩后 30.4kB。



Alpine.js 的大多数接口，都是以声明式的方式来操作DOM的（属性绑定，事件监听，以及动画）。



![img](https://i2.wp.com/css-tricks.com/wp-content/uploads/2020/04/Screenshot-2020-04-18-at-13.58.59.png?fit=1024%2C507&ssl=1)



作为比较，Vue 的最小体积为 63.5kB，压缩后为 22.8kB。两者的 API 相同，但为何 Alpine.js 的体积更加小巧？因为 Alpine.js 没有实现虚拟 DOM。相反，它直接更改 DOM，同时暴露与 Vue 相同的声明式 API。



#### 来看一个例子



使用 Alpine.js  是紧凑的，因为所编写的代码是声明式的，通过模板进行声明。下面以一个宝可梦搜索页面的例子来演示：



```
<div class="flex flex-col md:flex-row">
  <div x-data="pokeSearch()" x-init="fetchPokemon()" class="md:w-1/3 flex flex-col p-10">
    <div class="flex flex-row">
      <input type="text" name="pokemonSearch" x-model="pokemonSearch" class="flex w-2/3 bg-white focus:outline-none focus:shadow-outline border border-gray-300 rounded-lg py-2 px-4 appearance-none leading-normal" />
      <button type="submit" @click="fetchPokemon()" class="flex bg-blue-500 text-white font-bold py-2 px-4 rounded" :class="[ isLoading ? 'opacity-50 cursor-not-allowed' : 'hover:bg-blue-700' ]" :disabled="isLoading">
        Search
      </button>
    </div>
    <template x-if="pokemon">
      <div class="flex flex-row pt-10">
        <div class="flex mr-4">
          <img :src="pokemon.sprites.front_default" :alt="pokemon.name" />
        </div>
        <div class="text-sm justify-center flex flex-col">
          <h3 class="text-gray-900 text-sm font-bold uppercase leading-none mb-2" x-text="pokemon.name"></h3>
          <div class="flex flex-row flex-wrap">
            <template x-for="abilityObj in pokemon.abilities" :key="abilityObj.ability.url">
              <span x-text="abilityObj.ability.name" class="flex bg-gray-200 rounded-full px-3 py-1 text-xs font-semibold text-gray-700"></span>
            </template>
          </div>
        </div>
      </div>
    </template>
  </div>
</div>
```



```
function pokeSearch() {
  return {
    pokemonSearch: "charizard",
    pokemon: null,
    isLoading: false,
    fetchPokemon() {
      this.isLoading = true;
      fetch(`https://pokeapi.co/api/v2/pokemon/${this.pokemonSearch}`)
        .then(res => res.json())
        .then(data => {
          this.isLoading = false;
          this.pokemon = data;
        });
    }
  };
}
```

这个例子展示了组件如何使用`x-data`，以及通过方法返回组件的初始数据，定义方法，以及`x-init`指令指定的在组件加载完毕后执行的函数。



在 Alpine.js 中，绑定以及事件监听的语法与 Vue 类似：

- `Alpine`：`x-bind:attribute="express"` 和 `x-on:eventName="expression"`，缩写为：`:attribute="expression"`和`@eventName="expression"`。
- `Vue`：`v-bind:attribute="express"` 和 `v-on:eventName="expression"`, 缩写为：`:attribute="expression"` 和 `@eventName="expression"`



列表渲染通过 `x-for` 配合 `template`  元素，条件控制使用 `x-if` 配合 `template` 元素。

注意，alpine.js 并不提供完整的模板语言支持，所以无法使用嵌入语法（比如 Vue.js 中的 `{{ myValue }}`，及Handlebars 和 AngularJS 中的类似语法）。作为替代，alpine.js 使用 `x-text` 和 `x-html` 语句来实现（通过调用 `Node.innerText` 和 `Node.innerHTML`）。



使用 jQuery 实现相同功能，传统风格，需要以下几个步骤：

- 使用 `$('button').click(/* callback */)` 来绑定按钮点击事件。
- 在回掉函数中，得到输入组件 DOM 的值，然后去掉用接口。
- 掉用完成后，根据 API 的返回值，将新生成的节点更新上去。

如果你感兴趣一步一步的来比较同样功能的代码在 jQuery 和 Alpine.js 中的实现，[Alex Justesen](https://twitter.com/alexjustesen/status/1248286627467755520) 创建了一个相同功能的计数器，分别使用 [jQuery](https://jsfiddle.net/alexjustesen/4ant7cm6/) 和 [Alpine.js](https://jsfiddle.net/alexjustesen/97va6d1h/) 来实现。



#### 重新流行：以 HTML 为中心的工具



Alpine.js 从 TailwindCSS 的设计中获取灵感。Alpine.js 的介绍中提到，该库是“Tailwind 的 JavaScript 版”。



为什么这一点很重要？



Tailwind 的一个卖点是，它提供低级的工具类，让你有很方便可以构建你自己的　HTML　页面。这正是 Alpine 想实现的目标。它嵌入在 HTML 文件中，所以不需要再额外提供 JavaScript 模板，比如 Vue，React。甚至许多社区中使用 Alpine.js 的例子，连 script 标签都不需要。



让我们通过一个例子，来看看差异化。这是 Alpine.js 中，一个访问导航菜单的例子，它从头到尾都没有使用 script 标签。



```
<nav aria-labelledby="nav-heading" x-data="{ isOpen: false }" :aria-expanded="isOpen">
  <h2 id="nav-heading">Alpine.js Accessible Navigation</h2>
  <button :aria-expanded="isOpen" aria-controls="nav-list" @click="isOpen = !isOpen">
    Alpine.js a11y Navigation
  </button>
  <ul :hidden="!isOpen" x-cloak id="nav-list">
    <li>
      <a href="https://github.com/alpinejs/alpine">Alpine.js Docs</a>
    </li>
    <li>
      <a href="https://github.com/alpinejs/awesome-alpine">Awesome Alpine.js list</a>
    </li>
    <li>
      <a href="https://alpinejs.codewithhugo.com/newsletter">Alpine.js Weekly Newsletter</a>
    </li>
  </ul>
</nav>
```



```
[x-cloak] {
  display: none;
}
```



本示例利用Alpine.js外部的aria-labeledby和aria-controls（具有ID引用）。 Alpine.js确保“ toggle”元素（即按钮）具有一个aria-expanded属性，该属性在导航展开时为true，在折叠状态为false。 此aria展开的绑定也应用于菜单本身，我们通过绑定到隐藏来显示/隐藏其中的链接列表。



以标记为中心意味着Alpine.js和TailwindCSS示例易于共享。 它所要做的只是复制粘贴到HTML中，该文件也正在运行Alpine.js / TailwindCSS。 没有疯狂的目录，没有可编译并渲染为HTML的模板！



由于HTML是Web的基本构建块，因此Alpine.js是增强服务器渲染（Laravel，Rails，Django）或静态站点（Hugo，Hexo，Jekyll）的理想选择。 通过将一些JSON输出到x-data =“ {}”绑定中，将数据与这种工具集成起来很简单。 从后端/静态站点模板直接将一些JSON传递到Alpine.js组件中，避免了构建“另一个API端点”，而该API端点仅提供JavaScript小部件所需的数据片段。