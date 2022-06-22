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



上面的 `React.createElement()` 方法，返回下面的对象：



```
{
  type: 'div',
  props: {
    children: 'Login',
    id: 'login-btn'
  }
}
```



最终，使用 `ReactDOM.render` 来渲染 DOM：



```
<div id="login-btn">Login</div>
```



而组件可以用几种不同的方式声明。它可以是一个具有render()方法的类。或者，在简单的情况下，它可以定义为一个函数。在这两种情况下，它都以props作为输入，并返回JSX树作为输出：



```
const Button = ({ onLogin }) => (
  <div id={'login-btn'} onClick={onLogin}>
    Login
  </div>
);
```



JSX 被转译为一个 `React.createElement()` 函数树：



```
const Button = ({ onLogin }) =>
  React.createElement('div', { id: 'login-btn', onClick: onLogin }, 'Login');
```



#### 如何在 React 中创建组件？



有两种方式创建 React 组件。



1. `函数组件`：这是创建组件的最简单方法。这些是纯JavaScript函数，接受道具对象作为第一个参数并返回React元素：

```
function Greeting({ message }) {
  return <h1>{`Hello, ${message}`}</h1>;
}
```



2. `类组件`：你可以通过 ES6 类的方式来定义组件。上面的组件，使用类的方式来编写：



```
class Greeting extends React.Component {
  render() {
    return <h1>{`Hello, ${this.props.message}`}</h1>;
  }
}
```



#### 什么时候使用类组件来替换函数组件？



如果组件需要状态或者生命周期函数，使用类组件。其他情况，使用函数组件。然而，自 React 16.8 开始，可以使用引入的 React Hooks，来控制状态、生命周期函数等类组件的特性。



#### 什么是纯组件



`React.PureComponent` 与 `React.Component`，除了 `shouldComponentUpdate()` 函数的处理，其他完全相等。当 `props` 或者 `state` 发生改变，PureComponent 会与其进行一个浅比较（内存地址）。其他情况下，组件不会将当前的 props 和 state 新的值进行比较。因此，`shouldComponentUpdate` 的调用得到减少。当调用 `shouldComponentUpdate` 时，默认情况下，组件将重新渲染。



