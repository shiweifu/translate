翻译自：[300+ React Interview Questions - DEV Community](https://dev.to/sakhnyuk/300-react-interview-questions-2ko4)



### React 核心问题



#### 什么是 React？



React 是一个 `开源前端 JavaScript 库`，用于构建用户界面，特别是单页面应用。它应用于处理 Web 和 移动应用的视图层。React 的作者是 [Jordan Walke](https://github.com/jordwalke)，一个 Facebook 工作的软件工程师。React 在 2011 的 Facebook 新闻提要发布，2012 年，应用于 Instgram。



#### React 的主要特性是？



以下是 React 的主要特性：



- 使用虚拟 DOM，代替真实DOM，这是考虑到真实的 DOM 操作非常昂贵。
- 支持服务端渲染。
- 遵循单向数据流或数据绑定。
- 使用可重复使用/可复合的 UI 组件，来开发视图。



#### 什么是 JSX



JSX 是一个类似 XML 语法的 ECMAScript 扩展（首字母代表 JavaScript XML 的缩写）。简而言之，它只是为 `React.createElement()` 函数提供了语法糖，使我们像模板语法一样，具有 JavaScript 和 HTML 表达的能力。



在下面的例子中，`<h1>` 标记内的文本，作为 JavaScript 函数，返回给渲染函数。



```
class App extends React.Component {
  render() {
    return (
      <div>
        <h1>{'Welcome to React world!'}</h1>
      </div>
    );
  }
}
```



#### 组件和元素有什么不同？



Element（元素）是一个普通对象，描述您希望在屏幕上显示的 DOM 节点或其他组件。元素可以在其内部中包含其他元素。创建 React 元素很成本很低。一旦创建了一个元素，它就永远不能发生变化。



React Element的对象表示如下所示：



```
const element = React.createElement('div', { id: 'login-btn' }, 'Login');
```



