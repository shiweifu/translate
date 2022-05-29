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

