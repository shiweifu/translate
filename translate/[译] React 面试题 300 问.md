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



#### HTML 事件和 React 事件处理之间有什么不同？



下面是 HTML 事件处理和 React 事件处理的一些主要区别：



1. HTML 中，事件名字是小写：

```
<button onclick="activateLasers()"></button>
```



React 的事件，是驼峰格式：

```
<button onClick={activateLasers}>
```



2. 在 HTML 中，你可以返回 `false`，来阻止事件默认行为：

```
<a href="#" onclick='console.log("The link was clicked."); return false;' />
```



在 React 中，你必须明确调用 `preventDefault()`：



```
function handleClick(event) {
  event.preventDefault();
  console.log('The link was clicked.');
}
```



3. 在 HTML 中，你调用函数需要带上 `()`，而在 React 中，你不需要添加 `()` 在函数名称后面。（例如，请参考第一点中的 `activateLasers` 函数）。



#### 如何在 JSX 回调中，绑定函数或者事件处理程序？



有三种方式，来实现这个需求：



1. `在构造函数中绑定：` 在 JavaScript 类中，默认情况下，方法是不会被绑定的。该规则同样也适用于定义自定义类方法的 React 事件处理程序。通常我们在构造函数中，绑定它们。

   

```
class Component extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    // ...
  }
}
```



2. 公共类字段语法：如果你不喜欢使用绑定方法，那么可以使用公共类字段的语法，来正确绑定回调。

   

```
handleClick = () => {
  console.log('this is:', this);
};
```

```
<button onClick={this.handleClick}>{'Click me'}</button>
```



3. 回调使用箭头函数：你可以在回调中，直接使用箭头函数。



```
<button onClick={(event) => this.handleClick(event)}>{'Click me'}</button>
```



> 注意：如果回调通过 prop ，传递给子组件，那些组件可能被重新渲染（渲染多次）。这些情况下，考虑到性能，最好使用 `bind()` 或者公共类字段语法的方法来绑定。



#### 如何传递参数到一个事件处理器或者回调？



你可以使用箭头方法来封装一个事件处理器，并传递参数：

```
<button onClick={() => this.handleClick(id)} />
```



等同于调用 `.bind`：



```
<button onClick={this.handleClick.bind(this, id)} />
```



除了这两种方式以外，你也可以把方法作为参数，传递给箭头函数：



```
<button onClick={this.handleClick(id)} />;
handleClick = (id) => () => {
  console.log('Hello, your ticket number is', id);
};
```



#### React 中的合成事件是指什么？



`SyntheticEvent` 是一个针对不同浏览器的事件，所作的一个跨浏览器的封装。它的 API 接口与浏览器的本地事件相同，包括 `stopPropagation()` 以及 `preventDefault()` 这些事件，只是这些事件在所有浏览器中都是相同的。



#### 什么是内联条件表达式？



你可以使用if语句或从JS中获得的三元表达式来有条件地呈现表达式。除了这些方法之外，您还可以将任何表达式嵌入JSX中，方法是将它们包装在花括号中，然后再使用JS逻辑运算符&&。



```
<h1>Hello!</h1>;
{
  messages.length > 0 && !isLogin ? (
    <h2>You have {messages.length} unread messages.</h2>
  ) : (
    <h2>You don't have unread messages.</h2>
  );
}f
```



#### 什么是 `key` prop？它在数组中使用，有什么好处？



`key` 是在创建元素数组时应该包含的特殊字符串属性。关键道具帮助 React 识别哪些项目被更改、添加或删除。



通常我们使用对象的 ID 作为 Key：



```
const todoItems = todos.map((todo) => <li key={todo.id}>{todo.text}</li>);
```



当渲染数组条目，但并没有合适的 ID 作为 Key 的时候，可以使用索引作为 Key：



```
const todoItems = todos.map((todo, index) => <li key={index}>{todo.text}</li>);
```



##### 提示：



1. 如果 Item 的顺序会改变，则使用索引作为 key，是不推荐的。这可能对性能产生负面影响，并可能导致组件状态问题。

2. 如果将列表项提取为单单独的组件，那么将键应用于列表组件，而不是 `li` 标签。
3. 如果渲染一个列表，而 `key` 没有传递，则在终端中会打印错误信息。



#### refs 的作用是什么？



`refs` 用于引用一个元素。大多数情况下，用不上这种方式，但当你想直接访问 DOM 组件或者一个组件实例的时候，这很有用。



#### 如何创建 refs？



有两种方式创建 refs



1. 这是最近添加的一种方式。`Refs` 使用 `React.createRef()` 方法来创建，然后通过 `ref` 关键字，来关联 React 元素。为了在整个组件中使用 `ref`，只需要在构造函数中，将 `ref` 赋值给 `实例` 即可。



```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```



2. 无论React版本如何，你也可以使用ref回调方法。例如，搜索栏组件的输入元素访问如下：



```
class SearchBar extends Component {
  constructor(props) {
    super(props);
    this.txtSearch = null;
    this.state = { term: '' };
    this.setInputSearchRef = (e) => {
      this.txtSearch = e;
    };
  }
  onInputChange(event) {
    this.setState({ term: this.txtSearch.value });
  }
  render() {
    return (
      <input
        value={this.state.term}
        onChange={this.onInputChange.bind(this)}
        ref={this.setInputSearchRef}
      />
    );
  }
}
```



你也可以在使用闭包构建的函数组件中，使用 `refs` 。注意：您也可以使用内联引用回调，尽管这不是一个推荐的方法。



#### 什么是引用转发？



引用转发是一项特性，它允许一些组件，接收引用，并将其进一步传递给子组件。



```
const ButtonElement = React.forwardRef((props, ref) => (
  <button ref={ref} className="CustomButton">
    {props.children}
  </button>
));

// Create ref to the DOM button:
const ref = React.createRef();
<ButtonElement ref={ref}>{'Forward Ref'}</ButtonElement>;
```



#### 引用回调和寻找DOM节点，哪种做法是更推荐的？

 

使用回调 refs 优于 findDOMNode() API。因为 findDOMNode() 阻止了 React 将来的某些改进。



使用 findDOMNode 的传统做法：



```
class MyComponent extends Component {
  componentDidMount() {
    findDOMNode(this).scrollIntoView();
  }

  render() {
    return <div />;
  }
}
```



推荐的做法：



```
class MyComponent extends Component {
  constructor(props) {
    super(props);
    this.node = createRef();
  }
  componentDidMount() {
    this.node.current.scrollIntoView();
  }

  render() {
    return <div ref={this.node} />;
  }
}
```



#### 为什么是字符串引用特性遗留下来的？



如果您以前使用过React，您可能熟悉较老的API，其中ref属性是一个字符串，比如ref={'textInput'}， DOM节点通过this.ref .textInput访问。我们不建议这样做，因为字符串引用有以下问题，并且被认为是遗留问题。字符串ref在React v16中被删除。



1. 它们强制React跟踪当前正在执行的组件。这是有问题的，因为它使react模块有状态，因此当react模块在包中重复出现时，会导致奇怪的错误。
2. 如果库在传递的子对象上放置一个引用，则它们是不可组合的，用户不能在其上放置另一个引用。回调引用是完全可组合的。
3. 它们不适合像Flow这样的静态分析。流无法猜到框架使字符串引用出现在此的魔力。以及它的类型(可以是不同的)。回调引用对静态分析更友好。
4. 它不像大多数人期望的“渲染回调”模式那样工作(例如)。



```
class MyComponent extends Component {
  renderRow = (index) => {
    // This won't work. Ref will get attached to DataTable rather than MyComponent:
    return <input ref={'input-' + index} />;

    // This would work though! Callback refs are awesome.
    return <input ref={(input) => (this['input-' + index] = input)} />;
  };

  render() {
    return <DataTable data={this.props.data} renderRow={this.renderRow} />;
  }
}
```



#### 什么是虚拟 DOM



虚拟DOM (VDOM)是真实DOM的内存表示。UI的表示保存在内存中，并与“真正的”DOM保持同步。这是一个发生在调用渲染函数和在屏幕上显示元素之间的步骤。这整个过程叫做和解。g



#### 虚拟 DOM 如何工作？



虚拟 DOM 工作原理可以概括为三步：



1. 每当底层数据发生变化时，整个UI都将以Virtual DOM表示方式重新呈现。

![Alt Text](https://res.cloudinary.com/practicaldev/image/fetch/s--KTTPlm0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/3e7nivcb8kfqiewnzs66.png)



2. 然后计算前一个DOM表示和新DOM表示之间的差异。

![Alt Text](https://res.cloudinary.com/practicaldev/image/fetch/s--RWG1iA-z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/a3fw7zi3hq5a8of38so3.png)

3. 一旦计算完成，实际的DOM将只更新实际更改的内容。



#### 影子 DOM 和 虚拟 DOM 有什么不同？



Shadow DOM是一种浏览器技术，主要用于Web组件中的范围范围变量和CSS。虚拟DOM是库API顶部JavaScript中库实现的概念。



#### 什么是 React Fiber？



Fiber是React v16中核心算法的新协调引擎或重新实现。React Fiber的目标是提高它在动画、布局、手势、暂停、中止或重用工作等方面的适应性，并为不同类型的更新分配优先级;以及新的并发原语。



#### React Fiber 的主要目标是什么？



React Fiber的目标是提高它在动画、布局和手势等领域的适用性。它的主要特性是增量渲染:能够将渲染工作分成块，并将其分散到多个帧中。



#### 什么是受控组件？



控制表单中后续用户输入的输入元素的组件称为受控组件(Controlled component)，也就是说，每个状态突变都有一个相关的处理函数。



例如，要将所有名称都写成大写字母，我们使用如下所示的 `handleChange`：

```
handleChange(event) {
this.setState({value: event.target.value.toUpperCase()})
}
```



#### 什么是非受控组件？



`非受控组件`（controlled Components）在内部存储它们自己的状态，当你需要它时，你可以使用引用来查询DOM，以找到它的当前值。这有点像传统的 HTML。



在下面的 UserProfile 组件中，`name` 输入框通过 `ref` 来访问它。



```
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          {'Name:'}
          <input type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```



在大多数情况下，建议使用受控组件来实现表单。



#### createElement 和 cloneElement 之间的区别是什么？



JSX 元素将被转换为 React. createelement() 函数，以创建用于 UI 对象表示的 React 元素。而 cloneElement 用于克隆一个元素并向其传递新的参数。



#### 什么 React 中的是上升状态？



当多个组件需要共享相同的更改数据时，建议将共享状态提升到它们最近的公共祖先。这意味着如果两个子组件共享来自父组件的相同数据，那么将状态移到父组件，而不是在两个子组件中维护本地状态。



#### 组件包括哪些生命周期阶段？



组件的生命周期包括以下阶段：



1. 挂载：组件已经准备好挂载到浏览器DOM中。这个阶段涵盖了`构造函数()`、`getDerivedStateFromProps()`、`render()`和`componentDidMount()`生命周期方法的初始化。
2. 更新：在这个阶段，组件有两种更新方式，发送新属性和从 `setState()` 或 `forceUpdate()` 更新状态。这个阶段涵盖了 `getDerivedStateFromProps()`，`shouldComponentUpdate()`，`render()`， `getSnapshotBeforeUpdate()`和`componentDidUpdate()`生命周期方法。
3. 取消挂载：在最后这个阶段，不需要组件，从浏览器 DOM 中卸载。这个阶段包括 `componentWillUnmount()` 生命周期方法。



值得一提的是，在对DOM应用更改时，React内部有一个阶段概念。它们分别如下：



1. 渲染：组件渲染时不会产生任何副作用。这适用于Pure组件，在此阶段，React可以暂停、中止或重新启动渲染。
2. 预提交：在组件将更改实际应用于DOM之前，有一个时刻允许React通过getSnapshotBeforeUpdate()从DOM读取数据。
3. 提交：React与DOM一起工作，分别执行用于挂载的componentDidMount()、用于更新的componentDidUpdate()和用于卸载的componentWillUnmount()。

React 16.3 以上版本阶段图：



![Alt Text](https://res.cloudinary.com/practicaldev/image/fetch/s--x5MvIzGD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/drzx5s17mp2ac2zpnzk6.jpg)



16.3 之前

![Alt Text](https://res.cloudinary.com/practicaldev/image/fetch/s--ZPFk0rF0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/sb3po6oyw2cwmgvvvdvb.png)



#### React 的生命周期方法都有哪些？



16.3 之前：

- `componentWillMount`：在渲染前执行，用于根组件中的应用级配置。

- componentDidMount：在第一次渲染之后执行，这里所有的AJAX请求、DOM或状态更新以及设置事件监听器都将发生。
- componentWillReceiveProps：当特定参数更新以触发状态转换时执行。
- shouldComponentUpdate：确定组件是否要更新。默认情况下返回true。如果你确定组件在状态或道具更新后不需要渲染，你可以返回false值。这是一个提高性能的好地方，因为如果组件接收到新的道具，它允许你防止重新渲染。
- componentWillUpdate:当有属性和状态变化被 shouldComponentUpdate() 确认时，在重新渲染组件之前执行，它会返回true。
- componentDidUpdate：它主要用于更新DOM以响应道具或状态的变化。
- componentWillUnmount：它将用于取消任何传出的网络请求，或删除所有与该组件相关的事件监听器。















