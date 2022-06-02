翻译自：[CSS Shadow Parts - Style CSS Properties Inside of A Shadow Tree (ionicframework.com)](https://ionicframework.com/docs/theming/css-shadow-parts)



### CSS 影子组件



CSS 的影子组件，允许开发者将 CSS 属性，应用于内部的影子组件树中。这在定制 Ionic  框架影子组件树时，非常有用。



#### 为什么是影子组件？



Ionic 框架是 [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components) 的一个子集分发。Web 组件遵循  [影子组件规范](https://w3c.github.io/webcomponents/spec/shadow/)，以封装样式和标记。



> 备注
>
> Ionic 框架的组件，并不全是使用影子 DOM 实现的。如果该组件是使用影子 DOM 实现，文档右上方会有一个标记。使用 影子 DOM 实现组件的一个例子是按钮控件。



Shadow DOM 对于组织样式从组件中泄露，或者无意中应用到其他元素，非常有用。例如，我们将 `.button` 类赋值给 `ion-button`。如果没有 `shadow-dom` 的封装，如果用户在他们自己的元素上设置了 `.button`，它将被 `ionic` 的样式所覆盖。而使用 Shadow DOM 后的 `ion-button`，则没有这个问题。



然而，对于这种封装，样式也不能渗透到 Shadow Dom 组件的内部中。这意味着，如果一个 Shadow 组件，在它的影子树中，渲染元素，它的内部元素不能直接用 CSS 来处理。以 `ion-select` 组件作为例子，它有以下标记：



```
<ion-select>
  #shadow-root
  <div class="select-text select-placeholder"></div>
  <div class="select-icon"></div>
</ion-select>
```



占位符文本和图标元素在 `#shadow-root` 内部，这意味着以下 CSS 样式不能作用于该占位符：



```
/* Does NOT work */
ion-select .select-placeholder {
  color: blue;
}
```



那么，如何解决这个问题呢？[使用 CSS Shadow](https://ionicframework.com/docs/theming/css-shadow-parts#shadow-parts-explained)！



### Shaodw Parts 介绍



Shaodw parts 允许开发者，将样式应用在 shadow 树外部，应用到内部中。要实现这个目标，[part（部分） 必须暴露](https://ionicframework.com/docs/theming/css-shadow-parts#exposing-a-part)，并使用 [::part](https://ionicframework.com/docs/theming/css-shadow-parts#how-part-works)。



### 部分暴露



当创建一个 Shadow Dom 组件时，通过在元素上，指定一个 part 属性，可以将一个 part 添加到一个 Shadow 树的元素中。它被添加到 Ionic 框架的组件中，无需终端用户操作。



继续使用 `ion-select` 组件作为例子，标记更新后，如下所示：



```
<ion-select>
  #shadow-root
  <div part="placeholder" class="select-text select-placeholder"></div>
  <div part="icon" class="select-icon"></div>
</ion-select>
```



上面展示了两个部分：`placeholder` 和 `icon`。查看 [选择组件文档](https://ionicframework.com/docs/api/select#css-shadow-parts)，查看全部的 parts。



暴露了 parts 之后，现在可以直接实用 `::part` 对元素样式进行设置。



### Parts 如何工作



`::part()` 伪元素，允许开发者通过 part 属性，选择影子树之中的元素。



当我们了解 `ion-select` 暴露了 `placeholder` 部分，用于在没有选择值时，对文本设置样式，我们可以按照以下方式，进行设置：



```
ion-select::part(placeholder) {
  color: blue;
  opacity: 1;
}
```



使用 `::part`，允许当元素发生改变时，任意 CSS 属性，被接受。



此外，part 也支持使用伪类，在不显示暴露他们的情况下，进行样式化：



```
ion-select::part(placeholder)::first-letter {
  font-size: 22px;
  font-weight: 500;
}
```



`parts` 可以和大多数伪类一起工作：



```
ion-item::part(native):hover {
  color: green;
}
```



> 注意
>
> 带有[厂牌前缀的伪元素](https://ionicframework.com/docs/theming/css-shadow-parts#vendor-prefixed-pseudo-elements) ，和[结构化伪类](https://ionicframework.com/docs/theming/css-shadow-parts#structural-pseudo-classes)，有一些已知限制。



### Ionic 框架 Parts



Ionic 框架的组件，的所有暴露的 Parts，都可以在其 API 页面的 CSS Shadow parts 部分下找到。要查看所有组件及其API页面，请参阅[组件文档](https://ionicframework.com/docs/components)。



组件为了拥有 Parts，必须满足以下条件：



- 这是一个 [影子 DOM](https://ionicframework.com/docs/reference/glossary#shadow)。如果他是一个 [存在局部范围](https://ionicframework.com/docs/reference/glossary#scoped)，或者 DOM 组件，子元素可以被直接访问。如果该组件是一个局部范围或者影子组件，它将被列出在 [组件文档页](https://ionicframework.com/docs/components)。

- 它包含子元素。例如 `ion-card-header` 组件是一个影子组件，但它的所有样式，应用在它的 host CSS 属性上。因为它没有子组件，也就不需要使用 parts。

