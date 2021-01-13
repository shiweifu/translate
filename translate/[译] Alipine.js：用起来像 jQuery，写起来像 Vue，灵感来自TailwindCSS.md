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



jQuery 有一个核心特性，就是它封装了许多方法，让用户可以方便的实现各种动画效果，如 `slideUp`，`slideDown`，`fadeIn`，`fadeOut` 等。