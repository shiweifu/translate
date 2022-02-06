Stimulus 使用



Stimulus 是众多 JS 框架中的一个，它理念上的与众不同之处：



1. Rails 7 提供的前端环境（Hotwire）中的一部分，也是 DHH 钦定的前端框架
2. 和 React、Vue 等不同，它并不是一个【全栈】的前端框架，它希望页面渲染工作仍然放在后端，通过增强 HTML 的能力，来简化前端的工作



一段 Stimulus 代码：



```
<div data-controller="clipboard">
  PIN: <input data-target="clipboard.source" type="text" value="1234" readonly>
  <button data-action="clipboard#copy">Copy to Clipboard</button>
</div>
```







Stimulus 想做的事情和能做到的事情都比较少，因此它也足够简单，它使用控制器来连接 dom，除此之外，它的核心概念只有三个：



- action：通过 `data-action` 属性，来指定要出发的行为
- target：
- values



