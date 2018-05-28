### 安装cli
```
npm install -g create-react-app
create-react-app my-app
```

### 为什么不可变性在React当中非常重要

改变应用数据的方式一般分为两种。第一种是直接修改已有的变量的值。第二种则是将已有的变量替换为一个新的变量。

```
// 直接修改数据
var player = {score: 1, name: 'Jeff'};
player.score = 2;
// Now player is {score: 2, name: 'Jeff'}

-----------------------------------------------------

// 替换修改数据
var player = {score: 1, name: 'Jeff'};

var newPlayer = Object.assign({}, player, {score: 2});
// Now player is unchanged, but newPlayer is {score: 2, name: 'Jeff'}

// 或者使用最新的对象分隔符语法，你可以这么写：
// var newPlayer = {...player, score: 2};

```

两种方式的结果是一样的，但是第二种并没有改变之前已有的数据。通过这样的方式，我们可以得到以下几点好处：

1. 很轻松地实现 撤销/重做以及时间旅行

运用不可变性原则可以让我们很容易实现一些复杂的功能。例如我们在这个教程中会实现的，通过点击列表中的某一项直接返回当某一步棋时的状态。不改变已有的数据内容可以让我们在需要的时候随时切换回历史数据。

2. 记录变化

在我们直接修改一个对象的内容之后，是很难判断它哪里发生了改变的。我们想要判断一个对象的改变，必须拿当前的对象和改变之前的对象相互比较，遍历整个对象树，比较每一个值，这样的操作复杂度是非常高的。

而运用不可变性原则之后则要轻松得多。因为我们每次都是返回一个新的对象，所以只要判断这个对象被替换了，那么其中数据肯定是改变了的。

babel-plugin-transform-decorators-legacy
babel-plugin-syntax-decorators

### React类方法绑定`this`
绑定很重要，因为类方法不会自动绑定`this`到实例上。
类方法正确绑定`this`的方式：

1. 在构造函数中绑定（官网方式）：在构造函数中绑定时，绑定只会在组件实例化时运行一次
```
class AnyComponent extends Component {
  constructor(props) {
    super(props)
    this.onClickHandle = this.onClickHandle.bind(this)
  }
  onClickHandle() {
    .......
  }
  render() {
    return (
      <button 
        onClick={this.onClickHandle}
        type="button"
        >
        Button
      </button>  
    )
  }
}
```

2. 用`bind`方式绑定事件：避免这样做，因为它会在每次`render()`方法执行时绑定类方法。总结来说组件每次运行更新时都会导致性能消耗
```
class AnyComponent extends Component {
  onClickHandle() {
    .......
  }
  render() {
    return (
      <button 
        onClick={this.onClickHandle.bind(this)}
        type="button"
        >
        Button
      </button>  
    )
  }
}
```

3. 在构造函数中定义业务逻辑类方法：避免这样，因为随着时间的推移它会让你的构造函数变得混乱
```
class AnyComponent extends Component {
  constructor(props) {
    super(props)
    this.onClickHandle = () => {
     ......
    }
  }
  
  render() {
    return (
      <button 
        onClick={this.onClickHandle}
        type="button"
        >
        Button
      </button>  
    )
  }
}
```
4. 可以利用ES6箭头函数绑定：
```
class AnyComponent extends Component {
  constructor(props) {
    super(props)
  }
  onClickHandle = () => {
     ......
  }
  render() {
    return (
      <button 
        onClick={this.onClickHandle}
        type="button"
        >
        Button
      </button>  
    )
  }
}
```

### React事件处理
当需要传递参数到类的方法中，需要将方法封装在另一个箭头函数中，即`onClick={()=>this.clickHandle(param)}`方式,
当使用`onClick={doSomething()}`时,`doSomething()`函数会在浏览器打开程序时立即执行，由于监听表达式是函数执行的返回值而不再
是函数，所以点击按钮时不会有任何事发生。 但当使用`onClick={doSomething}`时，因为`doSomething`是一个函数，所以它会在点击按钮
时执行。
同样的规则也适用于在程序中使用的`onDismiss()`类方法。然而，使用`onClick={this.onDismiss}`并不够，因为这个类方法需要接收`param`
属性来识别那个将要被忽略的项，这就是为什么它需要被封装到另一个函数中来传递这个属性。

### React受控组件和非受控组件
表单元素比如`<input>`,`<textarea>` 和 `<select>` 会以原生 `HTML` 的形式保存他 们自己的状态。一旦有人从外部做了一些修改，它们就会修改内部的值，它们自己处理状态,在`React`中这被称为不受控组件。将表单元素的`value`交由`react`组件控制即可，输入框的单项数据流循环是自包含的，组件内部状态是输入框的唯一数据来源，即为受控组件。
```
class App extends Component {
  constructor(props) {
    super(props)
    this.state = {
      searchTerm: ''
    }
    this.onSearchChange = this.onSearchChange.bind(this)
  }
  onSearchChange (event) {
    this.setState(
      { searchTerm: event.target.value }
    )
  }
  render () {
    const { searchTerm, list } = this.state
    return (
      <div className="App">
        <form >
          <input
            type="text"
            // 将表单元素的value绑定到react组件的state中，组件内部状态是输入框的唯一数据来源
            value={searchTerm} 
            onChange={this.onSearchChange}
          />
        </form>
      </div>
    )
  }
}
```

### React不同的组件类型
• 函数式无状态组件: 这类组件就是函数，它们接收一个输入并返回一个输出。输入是`props`，输出就是一个普通的`JSX`组件实例。到这里，它和`ES6`类组件非常的相似。然而，函数式无状态组件是函数(函数式的)，并且它们没有本地状态(无状态的)。你不能通过`this.state`或者`this.setState()`来访问或者更新状态，因为这里没有`this`对象。此外，函数式无状态组件是没有生命周期方法的。

• `ES6`类组件:在类的定义中，它们继承自`React`组件。`extend`会注册所有的生命周期方法，只要在`React component API`中， 都可以在你的组件中使用。通过这种方式你可以使用`render()`类方法。此外，通过 使用`this.state`和`this.setState()`，你可以在 ES6 类组件中储存和操控`state`。

• `React.createClass`: 这类组件声明曾经在老版本的`React`中使用，仍然存在于很多`ES5 React`应用中。但是为了支持`JavaScript ES6`，`Facebook`声明它已经不推荐使用了。他们还在`React 15.5` 中加入了不推荐使用的警告70。

什么时候更适合使用函数式无状态组件而非`ES6`类组件?

一个经验法则就是当你不需要`本地状态`或者`组件生命周期方法`时，你就应该使用函数式无状态组件。最开始一般使用函数式无状态组件来实现你的组件，一旦你需要访问`state`或者`生命周期方法`时，你就必须要将它重构成一个`ES6`类组件。

### React生命周期

* `constructor(props)` - 它在组件初始化时被调用。在这个方法中，你可以设置初始化状态以及绑定类方法。
* `componentWillMount()` - 它在`render()`方法之前被调用。这就是为什么它可以用作去设置组件内部的状态，因为它不会触发组件的再次渲染。但一般来说，还是推荐在`constructor()`中去初始化状态。
* `render()` - 这个生命周期方法是必须有的，它返回作为组件输出的元素。这个方法应该是一个纯函数，因此不应该在这个方法中修改组件的状态。它把属性和状态作为输入并且返回(需要渲染的)元素
* `componentDidMount()` - 它仅在组件挂载后执行一次。这是发起异步请求去`API`获取数据的绝佳时期。获取到的数据将被保存在内部组件的状态中然后在 render() 生命 周期方法中展示出来。
* `componentWillReceiveProps(nextProps)`-这个方法在一个更新生命周`(updatelifecycle)`中被调用。新的属性会作为它的输入。因此你可以利用`this.props`来对比之后的属性和之前的属性，基于对比的结果去实现不同的行为。此外，你可以基于新的属性来设置组件的状态。
* `shouldComponentUpdate(nextProps, nextState)` - 每次组件因为状态或者属性更改而更新时，它都会被调用。你将在成熟的`React`应用中使用它来进行性能优化。在一个更新生命周期中，组件及其子组件将根据该方法返回的布尔值来决定是否重新渲染。 这样你可以阻止组件的渲染生命周期`(render lifecycle)`方法，避免不必要的渲染。
* `componentWillUpdate(nextProps, nextState)` - 这个方法是`render()`执行之前的最后一个方法。你已经拥有下一个属性和状态，它们可以在这个方法中任由你处置。你可以利用这个方法在渲染之前进行最后的准备。注意在这个生命周期方法中你不能再触发 setState()。如果你想基于新的属性计算状态，你必须利用`componentWillReceiveProps()`。
* `componentDidUpdate(prevProps, prevState)` - 这个方法在 render() 之后立即调用。你可以用它当成操作`DOM`或者执行更多异步请求的机会。
* `componentWillUnmount()` - 它会在组件销毁之前被调用。你可以利用这个生命周期方法去执行任何清理任务。

另一个生命周期方法:`componentDidCatch(error, info)`。它在 `React 16`中引入，用来捕获组件的错误。举例来说，在你的应用中展示样本数据本来是没问题的。但是可能会有 列表的本地状态被意外设置成 `null`的情况发生(例如从外部 `API`获取列表失败时，你把本 地状态设置为空了)。然后它就不能像之前一样去过滤(filter)和映射(map)这个列表，
因为它不是一个空列表(`[]`)而是 `null`。这时组件就会崩溃，然后整个应用就会挂掉。现 在你可以用 `componentDidCatch()` 来捕获错误，将它存在本地的状态中，然后像用户展示一条信息，说明应用发生了错误。

### Refs使用
React v16.3 引入的 React.createRef() API

使用 React.createRef() 创建 refs，通过 ref 属性来获得 React 元素。当构造组件时，refs 通常被赋值给实例的一个属性，这样你可以在组件中任意一处使用它们.

``` javascript
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
在 v16.3以前的版本，ref设置成字符串，在未来版本会被移除，建议使用回调的方式,官网列子如下：

``` javascript
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;

    this.setTextInputRef = element => {
      this.textInput = element;
    };

    this.focusTextInput = () => {
      // 直接使用原生 API 使 text 输入框获得焦点
      if (this.textInput) this.textInput.focus();
    };
  }

  componentDidMount() {
    // 渲染后文本框自动获得焦点
    this.focusTextInput();
  }

  render() {
    // 使用 `ref` 的回调将 text 输入框的 DOM 节点存储到 React
    // 实例上（比如 this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```


##### 参考
[React官网](https://doc.react-china.org/docs/refs-and-the-dom.html)
