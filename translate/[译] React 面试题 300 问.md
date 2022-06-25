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



#### 什么是 React 的 state？

d

组件的状态是一个对象，它包含一些在组件生命周期内可能会更改的信息。我们应该总是尽量使我们的状态尽可能简单，并尽量减少有状态组件的数量。



让我们创建一个带有消息状态的用户组件



```
class User extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      message: 'Welcome to React world',
    };
  }

  render() {
    return (
      <div>
        <h1>{this.state.message}</h1>
      </div>
    );
  }
}
```



![state](https://res.cloudinary.com/practicaldev/image/fetch/s--jbyEa70---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/qtv1ex4jequcrlpjntgx.jpg)

State类似于props，但它是私有的，完全由组件控制。也就是说，除了拥有和设置它的组件外，其他组件都不能访问它。



#### 什么是 React 的 Props？



Props 是组件的输入。它们是单个值或包含一组值的对象，这些值在创建时使用类似于html标签属性的命名约定传递给组件。它们是从父组件传递到子组件的数据。



React 中的 Props，主要的目的是提供以下组件功能：



1. 向组件传递自定义数据
2. 触发状态改变
3. 在组件的 `render` 方法中，通过使用 `this.props.reactProp` 使用



举例来说，让我们通过 `reactProp` 来创建组件：



```
<Element reactProp={'1'} />
```



然后，此 `reactProp` （或者声明的任何内容）名称，成为附加到 React 本来已经存在的 props 对象，该对象最初已经存在于使用 React 库创建的所有组件上。



```
props.reactProp
```



#### 组件的状态和 props 有何不同？

`props`和`state`都是纯 JavaScript 对象。虽然它们都保存着影响渲染输出的信息，但它们的功能相对于组件是不同的。将道具传递给组件类似于函数参数，而状态在组件中管理类似于在函数中声明的变量。



#### 为什么我们不能直接更新状态值？



如果你尝试直接更新状态值，组件将不会被重新渲染。



```
//Wrong
this.state.message = 'Hello world';
```



正确的做法是，使用 `setState()` 方法。它调度对组件状态的更新。当状态发生变化时，组件通过重新呈现，进行响应。



```
//Correct
this.setState({ message: 'Hello World' });
```



> 注意：你可以在构造函数中或者使用最新的javascript类字段声明语法直接给状态对象赋值。



#### `setState()` 的回调函数参数，作用是什么？



当 setState 执行完成，并渲染完成组件时，将调用回调函数。因为setState()是异步的，所以回调函数用于任何操作完成之后。



> 注意：建议使用声明周期相关的方法，而不是这个回调。

















