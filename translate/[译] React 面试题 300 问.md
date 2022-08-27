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



React 16.3+

- getDerivedStateFromProps：在调用render()之前调用，并在每次渲染时调用。这只存在于需要派生状态的极少数用例中。如果你需要导出状态，值得一读。
- componentDidMount：在第一次渲染之后执行，这里所有的AJAX请求、DOM或状态更新以及设置事件监听器都将发生。
- shouldComponentUpdate:确定组件是否要更新。默认情况下返回true。如果你确定组件在状态或道具更新后不需要渲染，你可以返回false值。这是一个提高性能的好地方，因为如果组件接收到新的道具，它允许你防止重新渲染。
- getSnapshotBeforeUpdate:在提交输出到DOM之前执行。它返回的任何值都将被传递给componentDidUpdate()。这对于从DOM获取信息很有用，比如滚动位置。
- componentDidUpdate:它主要用于更新DOM以响应道具或状态的变化。如果shouldComponentUpdate()返回false则不会触发。
- componentWillUnmount用于取消任何传出的网络请求，或删除所有与该组件相关的事件监听器。



#### 什么是高阶组件？



高阶组件(HOC)是接受组件并返回新组件的函数。简单来说，这是一个源自React的组合特性的模式。



我们称它们为纯组件，因为它们可以接受任何动态提供的子组件，但它们不会修改或复制输入组件的任何行为。



```
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```



HOC 的一些使用场景：

1. 代码复用，逻辑和启动抽象
2. 渲染劫持
3. 状态抽象和操作。
4. 属性操作



#### 如何为 HOC 组件创建属性代理？



你可以向组件添加/编辑属性，使用属性代理模式：



```
function HOC(WrappedComponent) {
  return class Test extends Component {
    render() {
      const newProps = {
        title: 'New Header',
        footer: false,
        showFeatureX: false,
        showFeatureY: true,
      };

      return <WrappedComponent {...this.props} {...newProps} />;
    }
  };
}
```



#### 什么是上下文？

Context 提供了一种通过组件树传递数据的方法，而不必在每一层都手动传递 props。

例如，许多组件需要在应用程序中访问经过身份验证的用户、区域设置首选项、UI主题。

```
const { Provider, Consumer } = React.createContext(defaultValue);
```



#### 什么是子 prop？

Children是一种道具(this.props.children)，它允许你将组件作为数据传递给其他组件，就像你使用的其他道具一样。放在组件开始和结束标签之间的组件树将作为子组件传递给该组件。



在React API中有许多可用的方法来处理这个 prop。这些包括 `React.Children.map`，`React.Children.forEach `，`React.Children.only`，`React.Children.toArray`。



下面是一个使用子 prop 的一个例子：

```
const MyDiv = React.createClass({
  render: function () {
    return <div>{this.props.children}</div>;
  },
});

ReactDOM.render(
  <MyDiv>
    <span>{'Hello'}</span>
    <span>{'World'}</span>
  </MyDiv>,
  node,
);
```



#### 在 React 中，如何写注释？



React/JSX 中的注释，与 JavaScript 中的多行注释类似，但是包在大括号中的。



#### 单行注释：

```
<div>
  {/* Single-line comments(In vanilla JavaScript, the single-line comments are represented by double slash(//)) */}
  {`Welcome ${user}, let's play React`}
</div>
```



#### 多行注释：

```
<div>
  {/* Multi-line comments for more than
  one line */}
  {`Welcome ${user}, let's play React`}
</div>
```



### 在构造函数中，调用 `super` 方法，传递 props 作为参数的目的是什么？



子类构造函数在调用super()方法之前不能使用此引用。同样的情况也适用于ES6的子类。将props参数传递给super()调用的主要原因是为了访问这个参数。子构造函数中的道具。



#### 传递 props：



```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    console.log(this.props); // prints { name: 'John', age: 42 }
  }
}
```



#### 不传递 props：



```
class MyComponent extends React.Component {
  constructor(props) {
    super();

    console.log(this.props); // prints undefined

    // but props parameter is still available
    console.log(props); // prints { name: 'John', age: 42 }
  }

  render() {
    // no difference outside constructor
    console.log(this.props); // prints { name: 'John', age: 42 }
  }
}
```



上面的代码片段揭示了这一点。props 仅在构造函数中有所不同。在构造函数外也是一样的。



#### 什么是 reconciliation？



当组件的 props 或状态发生变化时，React 会通过比较新返回的元素，和之前渲染的元素，来决定是否需要进行真实的 DOM 更新。当它们不相等时，React将更新DOM。这个过程被称为 reconciliation。



#### 如何动态的设置键值名称？



如果您使用ES6或Babel transpiler来转换JSX代码，那么您可以使用计算属性名称来完成这一点。

```
handleInputChange(event) {
this.setState({ [event.target.id]: event.target.value })
}
```



#### 每次组件渲染时调用函数的常见错误是什么？



在将函数作为参数传递时，需要确保该函数没有被调用。

```
render() {
// Wrong: handleClick is called instead of passed as a reference!
return <button onClick={this.handleClick()}>{'Click Me'}</button>
}
```

相反，传递不带圆括号的函数本身。

```
render() {
// Correct: handleClick is passed as a reference!
return <button onClick={this.handleClick}>{'Click Me'}</button>
}
```



#### 惰性函数支持导出吗？



不,目前 `React.lazy` 函数只支持默认导出。如果要导入名为exports的模块，可以创建一个中间模块，将其作为默认值重新导出。它还确保摇树持续工作，不会拉出未使用的组件。让我们以导出多个命名组件的组件文件为例：



```
// MoreComponents.js
export const SomeComponent = /* ... */;
export const UnusedComponent = /* ... */;
```



接下来，在 `IntermediateComponent.js` 中，重新导出 `MoreComponents.js` 组件



```
// IntermediateComponent.js
export { SomeComponent as default } from './MoreComponents.js';
```



现在，你可以使用下面的方式，来导入惰性函数了：



```
import React, { lazy } from 'react';
const SomeComponent = lazy(() => import('./IntermediateComponent.js'));
```



#### 为什么 React 使用 `className` 来覆盖 `class` 属性？



`class` 是 JavaScript 中的关键字，JSX 是 JavaScript 的扩展。这就是 React 使用 `className` 而不是使用 `class` 的主要原因。`className` 属性也接收字符串：



```
render() {
return <span className={'menu navigation-menu'}>{'Menu'}</span>
}
```



#### 什么是片段？



片段是 React 中，一种常用的模式，返回多个元素。片段可以让你将一组子元素打包为一个列表，一起返回，而无需额外的 DOM 结构。



```
render() {
return (
  <React.Fragment>
    <ChildA />
    <ChildB />
    <ChildC />
  </React.Fragment>
)
}
```



还有一个更短的写法，但很多工具不支持。



```
render() {
return (
  <>
    <ChildA />
    <ChildB />
    <ChildC />
  </>
)
}
```



#### 为什么比起包裹在 div 之中，片段（Fragments）是一种更好的写法？



下面是一些理由：



1. Fragments 更快，而且不需要创建额外的DOM节点，因此使用的内存更少。这只对非常大和深的树有真正的好处。

2. 一些CSS机制，如Flexbox和CSS Grid有一种特殊的父子关系，在中间添加div会使其难以保持所需的布局。
3. DOM检查器不那么混乱。



#### 什么是 React 中的 portals？



Portal是将子组件呈现到父组件DOM层次结构之外的DOM节点的推荐方法。



```
ReactDOM.createPortal(child, container);
```



第一个参数是任何可呈现的React子元素，比如元素、字符串或片段。第二个参数是一个DOM元素。



#### 什么是无状态组件？



如果行为独立于它的状态，那么它可以是一个无状态组件。可以使用函数或类来创建无状态组件。但是，除非你需要在组件中使用生命周期钩子，否则你应该选择功能组件。如果你决定在这里使用功能组件会有很多好处;它们易于编写、理解和测试，速度更快，而且可以完全避免使用这个关键字。



#### 什么是有状态组件？



如果一个组件的行为依赖于该组件的状态，那么它可以被称为有状态组件。这些有状态组件始终是类组件，并且具有在构造函数中初始化的状态。



```
class App extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  render() {
    // ...
  }
}
```



#### React 16.8 更新：



Hook 使你可以使用 React 状态和其他特性，而无需编写类。



与之相等的函数组件：

```
import React, {useState} from 'react';

const App = (props) => {
  const [count, setCount] = useState(0);

  return (
    // JSX
  )
}
```



#### 在 React 中，如何验证属性？



当应用程序在开发模式下运行时，React会自动检查我们设置在组件上的所有道具，以确保它们具有正确的类型。如果类型不正确，React将在控制台中生成警告消息。由于性能的影响，它在生产模式中被禁用。强制道具是用isRequired定义的。



可验证的属性类型列表：



1. `PropTypes.number`
2. `PropTypes.string`
3. `PropTypes.array`
4. `PropTypes.object`
5. `PropTypes.func`
6. `PropTypes.node`
7. `PropTypes.element`
8. `PropTypes.bool`
9. `PropTypes.symbol`
10. `PropTypes.any`



我们可以为 `User` 组件，定义 `propTypes`，如下所示：



```
import React from 'react';
import PropTypes from 'prop-types';

class User extends React.Component {
  static propTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number.isRequired,
  };

  render() {
    return (
      <>
        <h1>{`Welcome, ${this.props.name}`}</h1>
        <h2>{`Age, ${this.props.age}`}</h2>
      </>
    );
  }
}
```



注意:在React v15.5中，PropTypes从React中移动。PropTypes到prop-types库。



#### React 的优势是什么？



下面是 React 的一些优势：



1. 使用Virtual DOM提高应用程序的性能。
2. JSX使代码易于读和写。
3. 它同时呈现在客户端和服务器端（SSR）。
4. 很容易与框架集成(Angular、Backbone)，因为它只是一个视图库。
5. 使用Jest等工具编写单元和集成测试很容易。



#### 什么是 React 的限制？



除了优点之外，React也有一些限制。



1. React只是一个视图库，而不是一个完整的框架。
2. 对于新的 Web 开发者，有一定学习门槛
3. 集成 React 到传统 MVC 项目，需要一些额外的配置
4. 使用内联模板和 JSX，导致复杂度增加
5. 太多的组件，导致过度工程化或者样板话



#### React v16 的边界错误是什么？



错误边界是指组件在其子组件树的任何地方捕获 JavaScript 错误，记录这些错误，并显示回退UI，而不是崩溃的组件树。



如果类组件定义了一个新的生命周期方法 componentDidCatch(error, info) 或静态 getDerivedStateFromError()，那么它就会成为错误边界。



```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  componentDidCatch(error, info) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, info);
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>{'Something went wrong.'}</h1>;
    }
    return this.props.children;
  }
}
```



标准组件中使用：



```
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```



#### 如何在 React v15 中使用边界错误？



React v15，对于边界错误，通过使用 `unstable_handleError` 方法，提供非常基本的支持。在 React v16 中，它已经改名为 `componentDidCatch`。



#### 关于静态类型检查，推荐的方法有哪些？



通常我们使用PropTypes库(React。PropTypes从React v15.5开始移动到prop-types包)，用于在React应用程序中进行类型检查。对于大型代码库，建议使用静态类型检查器，如Flow或TypeScript，它们在编译时执行类型检查，并提供自动完成功能。



#### `react-dom` 包有什么用？



react-dom包提供了特定于dom的方法，可以在应用程序的顶层使用。大多数组件都不需要使用此模块。这个包的一些方法是：



1. `render()`
2. `hydrate()`
3. `unmountComponentAtNode()`
4. `findDOMNode()`
5. `createPortal()`



#### 渲染方法的目的是什么？



此方法用于在提供的容器中将React元素呈现到DOM中，并返回对组件的引用。如果React元素之前被呈现到容器中，它将对其执行更新，并且仅根据需要改变DOM以反映最新的更改。



```
ReactDOM.render(element, container[, callback])
```



如果提供了可选回调函数，它将在组件呈现或更新后执行。



### 什么是 ReactDOMServer？



ReactDOMServer对象使您能够将组件呈现为静态标记(通常用于节点服务器)。该对象主要用于服务器端渲染(SSR)。以下方法可以在服务器和浏览器环境中使用：



1. `renderToString()`
2. `renderToStaticMarkup()`



例如，你通常运行一个基于节点的web服务器，如Express, Hapi，或Koa，你调用 `renderToString` 将根组件渲染成一个字符串，然后作为响应发送。



```
// using Express
import { renderToString } from 'react-dom/server';
import MyPage from './MyPage';

app.get('/', (req, res) => {
  res.write('<!DOCTYPE html><html><head><title>My Page</title></head><body>');
  res.write('<div id="content">');
  res.write(renderToString(<MyPage />));
  res.write('</div></body></html>');
  res.end();
});
```



#### 如何在 React 中，使用 innerHTML



`dangerouslySetInnerHTML` 属性是 React 在浏览器 DOM 中使用 innerHTML 的替代品。就像 innerHTML 一样，考虑到跨站点脚本(XSS) 攻击，使用这个属性是有风险的。你只需要传递一个 html 对象作为键，html 文本作为值。



本例中，`MyComponent` 使用 `dangerouslySetInnerHTML` 属性来设置 HTML 标签。



#### React 中，如何使用样式？



style 属性接受一个带有驼峰格式属性的 JavaScript 对象，而不是一个 CSS 字符串。这与 DOM 样式的 JavaScript 属性是一致的，更有效，并防止 XSS 安全漏洞。



```
const divStyle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')',
};

function HelloWorldComponent() {
  return <div style={divStyle}>Hello World!</div>;
}
```



为了与 JavaScript 中访问 DOM 节点上的属性保持一致（例如 node.style.backgroundImage），样式键是驼峰式的。



#### React 中的事件有何不同？



在React元素中处理事件有一些语法差异：

1. React事件处理程序使用驼峰case命名，而不是小写。
2. 在JSX中，您传递一个函数作为事件处理程序，而不是一个字符串。



#### 如果在构造函数中使用setState()会发生什么



当使用 setState() 时，除了给对象状态赋值外，React 还会重新呈现组件及其所有子组件。您将得到这样的错误：只能更新已安装或正在安装的组件。所以我们需要利用这个。状态来初始化构造函数中的变量。



#### 使用索引作为键值，会有什么影响？



键值在 React 中，应该是稳定的，可预测的以及唯一的，以便对元素进行跟踪。



在下面的代码片段中，每个元素的键是有序的，而不是和数据内容相关，这限制了 React 所能做的优化。



```
{
  todos.map((todo, index) => <Todo {...todo} key={index} />);
}
```



如果你使用元素数据，作为键值，假设 todo.id 对于列表，是唯一且稳定的，React 将能够重新排序元素，而不需要重新计算它们。



#### 在 `componentWillMount()` 方法中，使用 `setState()`，是否是好的实践？



是的，在componentWillMount()方法中使用setState()是安全的。但同时，建议在 componentWillMount() 生命周期方法中避免异步初始化。componentWillMount() 在挂载发生之前立即被调用。它在 render() 之前被调用，因此在此方法中设置 state 不会触发重新渲染。避免在此方法中引入任何副作用或订阅。我们需要确保组件初始化的异步调用发生在 componentDidMount() 而不是 componentWillMount() 中。



```
componentDidMount() {
axios.get(`api/todos`)
  .then((result) => {
    this.setState({
      messages: [...result.data]
    })
  })
}
```



#### 如果你在初始状态下使用 props 会发生什么



如果组件上的 props 被更改而组件没有被刷新，那么新的 props 将永远不会显示出来，因为构造函数永远不会更新组件的当前状态。props 的状态初始化只在组件第一次创建时运行。



下面的组件不会显示更新后的输入值。



```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      records: [],
      inputValue: this.props.inputValue,
    };
  }

  render() {
    return <div>{this.state.inputValue}</div>;
  }
}
```



在 `render` 方法中，使用 `props`，将更新对应的值：



```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      record: [],
    };
  }

  render() {
    return <div>{this.props.inputValue}</div>;
  }
}
```



#### 根据什么条件，判断是否渲染组件？



在某些情况下，您希望根据某些状态呈现不同的组件。JSX不会呈现为false或未定义，因此您可以使用条件短路来仅在某个条件为真时呈现组件的给定部分。



```
const MyComponent = ({ name, address }) => (
  <div>
    <h2>{name}</h2>
    {address && <p>{address}</p>}
  </div>
);
```



如果需要If -else条件，则使用三元操作符。



```
const MyComponent = ({ name, address }) => (
  <div>
    <h2>{name}</h2>
    {address ? <p>{address}</p> : <p>{'Address is not available'}</p>}
  </div>
);
```



#### 为什么在DOM元素上传递 props 时要小心？



当我们传递 props 时，我们会遇到添加未知HTML属性的风险，这是一种糟糕的做法。相反，我们可以使用道具解构`…Rest`操作符，因此它将只添加所需的道具。



举个例子，



```
const ComponentA = () => <ComponentB isDisplay={true} className={'componentStyle'} />;

const ComponentB = ({ isDisplay, ...domProps }) => <div {...domProps}>{'ComponentB'}</div>;
```



#### 如何在 React 中使用装饰器？



你可以装饰你的类组件，这和把组件传递给一个函数是一样的。装饰器是修改组件功能的灵活且易读的方式。



```
@setTitle('Profile')
class Profile extends React.Component {
  //....
}

/*
title is a string that will be set as a document title
WrappedComponent is what our decorator will receive when
put directly above a component class as seen in the example above
*/
const setTitle = (title) => (WrappedComponent) => {
  return class extends React.Component {
    componentDidMount() {
      document.title = title;
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
};
```



注意：装饰器是ES7中没有的一个特性，但目前是第二阶段的提案。



#### 如何使用记忆组件？



有一些可用的memoize库可用于函数组件。



例如moize库可以记忆其他组件中的组件。



```
import moize from 'moize';
import Component from './components/Component'; // this module exports a non-memoized component

const MemoizedFoo = moize.react(Component);

const Consumer = () => {
  <div>
    {'I will memoize the following entry:'}
    <MemoizedFoo />
  </div>;
};
```



更新:自React v16.6.0以来，我们有了一个React.memo。它提供了一个更高阶的组件来记忆组件，除非道具发生变化。要使用它，只需使用React包装组件，简单封装了之前使用的 `React.memo`。



```
const MemoComponent = React.memo(function MemoComponent(props) {
  /* render using props */
});
OR;
export default React.memo(MyFunctionComponent);
```



#### 如何实现服务器端渲染或SSR



React已经可以处理Node服务器上的呈现。DOM呈现程序的一个特殊版本是可用的，它遵循与客户端相同的模式。



```
import ReactDOMServer from 'react-dom/server';
import App from './App';

ReactDOMServer.renderToString(<App />);
```



该方法将常规HTML输出为字符串，然后可以将其放置在页面主体中作为服务器响应的一部分。在客户端，React会检测到预渲染的内容，并无缝地从停止的地方开始。



#### 如何在 React 中激活 production 模式？



你应该使用 Webpack 的 DefinePlugin 方法来将 NODE_ENV 设置为生产版本，这样就去掉了 propType 验证和额外的警告。除此之外，如果你缩小代码，例如，去除 Uglify 的死代码，去掉开发代码和注释，这将大大减少你的包的大小。



#### 什么是 CRA？有什么好处？



`create-react-app` CLI工具允许您快速创建和运行React应用程序，无需配置步骤。



让我们使用 CRA 创建 Todo App：



```
# Installation
$ npm install -g create-react-app

# Create new project
$ create-react-app todo-app
$ cd todo-app

# Build, test and run
$ npm run build
$ npm run test
$ npm start
```



它包含了我们构建React应用所需的一切：



1. React、JSX、ES6和Flow语法支持。
2. ES6之外的语言附加，如对象扩展操作符。
3. 自动修复CSS，所以你不需要-webkit-或其他前缀。
4. 一个快速的交互式单元测试运行器，内置了对覆盖率报告的支持。
5. 对常见错误发出警告的活动开发服务器。
6. 一个构建脚本，将JS、CSS和图像与散列和源地图捆绑在一起用于生产。



#### 组件的生命周期都包括什么？



当创建组件实例并将其插入到DOM中时，生命周期方法按以下顺序调用。

1. `constructor()`
2. `static getDerivedStateFromProps()`
3. `render()`
4. `componentDidMount()`



#### 在 React 16 中，哪些声明周期相关的方法被废弃？



以下生命周期方法将是不安全的编码实践，并且异步渲染将更加问题。



1. `componentWillMount()`
2. `componentWillReceiveProps()`
3. `componentWillUpdate()`



从React v16.3开始，这些方法使用了不安全的前缀，无前缀的版本将在React v17中删除。



#### getDerivedStateFromProps()生命周期方法的目的是什么



新的静态 `getDerivedStateFromProps()` 生命周期方法在组件实例化之后以及重新呈现之前被调用。它可以返回一个对象来更新状态，或者返回null来表示新道具不需要任何状态更新。



```
class MyComponent extends React.Component {
  static getDerivedStateFromProps(props, state) {
    // ...
  }
}
```



这个生命周期方法和`componentDidUpdate()`一起涵盖了`componentWillReceiveProps()`的所有用例。



#### getSnapshotBeforeUpdate() 生命周期方法的目的是什么



新的 `getSnapshotBeforeUpdate()` 生命周期方法在DOM更新之前被调用。这个方法的返回值将作为第三个参数传递给 `componentDidUpdate()`。



```
class MyComponent extends React.Component {
  getSnapshotBeforeUpdate(prevProps, prevState) {
    // ...
  }
}
```



这个生命周期方法和`componentDidUpdate()`一起涵盖了`componentWillUpdate()`的所有用例。



#### hook是否替换了渲染道具和更高阶的组件



渲染道具和高阶组件都只渲染一个子组件，但在大多数情况下，通过减少树中的嵌套，hook是一种更简单的服务方式。





#### 推荐的组件命名方式是什么



建议使用引用的方式命名组件，而不是使用`displayName`。



使用 `displayName` 用于命名组件：



```
export default React.createClass({
  displayName: 'TodoApp',
  // ...
});
```



`推荐` 的方法：



```
export default class TodoApp extends React.Component {
  // ...
}
```



#### 组件类中方法的推荐顺序是什么



建议从安装到渲染阶段的方法排序：



1. `static` 方法
2. `constructor()`
3. `getChildContext()`
4. `componentWillMount()`
5. `componentDidMount()`
6. `componentWillReceiveProps()`
7. `shouldComponentUpdate()`
8. `componentWillUpdate()`
9. `componentDidUpdate()`
10. `componentWillUnmount()`
11. 点击事件或者事件处理函数，如 `onClickSubmit()` 或者 `onChangeDescription()`
12. 渲染中，用到的获取属性的方法： `getSelectReason()` 或者 `getFooterContent()`
13. 可选渲染方法： `renderNavigation()` 或者 `renderProfilePicture()`
14. `render()`



#### #### 什么是切换组件



切换组件是呈现多个组件中的一个的组件。我们需要使用对象，将属性值映射到组件。



例如，切换组件根据页面道具显示不同的页面：



```
import HomePage from './HomePage';
import AboutPage from './AboutPage';
import ServicesPage from './ServicesPage';
import ContactPage from './ContactPage';

const PAGES = {
  home: HomePage,
  about: AboutPage,
  services: ServicesPage,
  contact: ContactPage,
};

const Page = (props) => {
  const Handler = PAGES[props.page] || ContactPage;

  return <Handler {...props} />;
};

// The keys of the PAGES object can be used in the prop types to catch dev-time errors.
Page.propTypes = {
  page: PropTypes.oneOf(Object.keys(PAGES)).isRequired,
};
```



#### 为什么我们需要向 setState 传递一个方法？



这背后的原因是setState()是一个异步操作。出于性能原因对批处理状态更改作出反应，因此在调用setState()后状态可能不会立即更改。这意味着在调用setState()时不应该依赖于当前状态，因为您无法确定当前状态是什么。解决方案是将一个函数传递给setState()，并将前一个状态作为参数。通过这样做，您可以避免由于setState()的异步特性而导致用户在访问时获取旧状态值的问题。



假设初始计数值为0。连续进行三次增量操作后，该值将只增加1。



```
// assuming this.state.count === 0
this.setState({ count: this.state.count + 1 });
this.setState({ count: this.state.count + 1 });
this.setState({ count: this.state.count + 1 });
// this.state.count === 1, not 3
```



如果我们将一个函数传递给`setState()`，计数器将正确地增加。



```
this.setState((prevState, props) => ({
  count: prevState.count + props.increment,
}));
// this.state.count === 3 as expected
```



或者



#### 为什么 `setState` 接受方法，优先于接受对象？



为了提高性能，React可以将多个setState()调用批量处理为一个更新。因为这个。props 和 state 可以异步更新，您不应该依赖它们的值来计算下一个状态。



下面的计数例子，将不会如你期待的那样工作：

```
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```



首选的方法是用函数而不是对象调用setState()。该函数将接收前一个状态作为第一个参数，应用更新时的 props 作为第二个参数。



```
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment,
}));
```



#### React 中的严格模式是什么？



反应。StrictMode是一个用来突出应用程序中潜在问题的有用组件。就像<Fragment>一样，<StrictMode>不渲染任何额外的DOM元素。它会为它的后代启动额外的检查和警告。这些检查只适用于开发模式。



```
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```



在上面的例子中，严格模式检查仅适用于<ComponentOne>和<ComponentTwo>组件。



###  什么是 React Mixins？



mixin是一种完全分离组件以拥有通用功能的方法。mixin不应该被使用，可以用高阶组件或装饰器替换。



最常用的mixin之一是`PureRenderMixin`。你可能会在一些组件中使用它来防止不必要的重新渲染，当道具和状态与之前的道具和状态粗略相等时：



```
const PureRenderMixin = require('react-addons-pure-render-mixin');

const Button = React.createClass({
  mixins: [PureRenderMixin],
  // ...
});
```



#### 为什么isMounted()是反模式，正确的解决方案是什么？



isMounted() 的主要用途是避免在组件卸载后调用 setState()，因为它会发出警告。

```
if (this.isMounted()) {
this.setState({...})
}
```



调用 `setState()` 之前，检查 `isMounted()`，确实可以消除警告，但这样会失去警告的目的。代码中出现 `isMounted()` 的唯一情况是，你想判断，是否有组件在 unmounted 之后，依然持有引用。



一个替代方案是，检查在组件 `unmounted` 之后，依然有哪些地方调用 `setState()`，找出他们，并修复。比较常见的一个调用点是，回调函数中，组件正在等待一些数据，然而在到达之前，已经卸载了。理想情况下，应该在 unmount 之前，在 componentWillUnmount() 中，取消任何回调。



#### React中支持的指针事件是什么



Pointer Events提供了处理所有输入事件的统一方式。在过去，我们有鼠标和相应的事件监听器来处理它们，但现在我们有许多设备与鼠标无关，如带有触控表面的手机或笔。我们需要记住，这些事件只能在支持指针事件规范的浏览器中工作。



以下事件类型现在可以在React DOM中使用：



1. `onPointerDown`
2. `onPointerMove`
3. `onPointerUp`
4. `onPointerCancel`
5. `onGotPointerCapture`
6. `onLostPointerCapture`
7. `onPointerEnter`
8. `onPointerLeave`
9. `onPointerOver`
10. `onPointerOut`



#### 为什么组件名称要以大写字母开头



如果使用JSX呈现组件，该组件的名称必须以大写字母开头，否则React将抛出一个错误，称其为无法识别的标记。这是因为只有HTML元素和SVG标记可以以小写字母开头。



```
class SomeComponent extends Component {
  // Code goes here
}
```



你可以定义以小写字母开头的组件，但当它被导入时，它必须以大写字母开头。在这里，小写字母是可以的：

```
class myComponent extends Component {
  render() {
    return <div />;
  }
}

export default myComponent;
```



当引入其他文件中的组件时，必须以大写字母开头：



```
import MyComponent from './MyComponent';
```



#### 组件名称有哪些例外规则？



组件名称应该以大写字母开头，但在此约定中也有一些例外。带有点的小写标记名称(属性访问器)仍然被认为是有效的组件名称。



例如，下面的标签可以被编译为有效的组件：



```
render(){
  return (
      <obj.component /> // `React.createElement(obj.component)`
      )
}
```



#### 在React v16中是否支持自定义DOM属性



答案是可以。在过去，React 会忽略未知的 DOM 属性。如果你在 JSX 中，写了一些 React 不能识别的属性，React 将忽略它。



举个例子，让我们看以下这段代码：



```
<div mycustomattribute={'something'} />
```



在 React v15 中，讲生成一个空的 `div`：



```
<div />
```



在 React v16 中，未知的属性，将放在 DOM 的结尾：



```
<div mycustomattribute="something" />
```



这对于提供特定于浏览器的非标准属性、尝试新的DOM api以及集成特定的第三方库非常有用。



#### 构造函数和getInitialState之间的区别是什么？



使用ES6类时应该在构造函数中初始化state，使用`React.createClass()`时应该在`getInitialState()`方法中初始化。



##### 使用 ES6 类：

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      /* initial state */
    };
  }
}
```



##### 使用 `React.createClass()`：

```
const MyComponent = React.createClass({
  getInitialState() {
    return {
      /* initial state */
    };
  },
});
```

`注意`：`React.createClass()` 方法已经废弃，在 React v16 中，被移除。使用标准的 JavaScript 类方法进行替代。



#### 可以在不调用setState的情况下强制组件重新呈现吗？



默认情况下，当组件的状态或 props 改变时，组件将重新渲染。如果 render() 方法依赖于其他数据，可以通过调用 forceUpdate() 告诉 React 组件需要重新渲染。































































