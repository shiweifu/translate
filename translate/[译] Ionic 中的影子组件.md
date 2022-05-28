翻译自：[CSS Shadow Parts - Style CSS Properties Inside of A Shadow Tree (ionicframework.com)](https://ionicframework.com/docs/theming/css-shadow-parts)



### CSS 影子组件



CSS 的影子组件，允许开发者将 CSS 属性，应用于内部的影子组件树中。这在定制 Ionic  框架影子组件树时，非常有用。



#### 为什么是影子组件？



Ionic 框架是 [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components) 的一个子集分发。Web 组件遵循  [影子组件规范](https://w3c.github.io/webcomponents/spec/shadow/)，以封装样式和标记。



> 备注
>
> Ionic 框架的组件，并不全是使用影子 DOM 实现的。如果该组件是使用影子 DOM 实现，文档右上方会有一个标记。使用 影子 DOM 实现组件的一个例子是按钮控件。