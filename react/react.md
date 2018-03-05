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

### react类方法绑定`this`
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

### react事件处理
当需要传递参数到类的方法中，需要将方法封装在另一个箭头函数中，即`onClick={()=>this.clickHandle(param)}`方式,
当使用`onClick={doSomething()}`时,`doSomething()`函数会在浏览器打开程序时立即执行，由于监听表达式是函数执行的返回值而不再
是函数，所以点击按钮时不会有任何事发生。 但当使用`onClick={doSomething}`时，因为`doSomething`是一个函数，所以它会在点击按钮
时执行。
同样的规则也适用于在程序中使用的`onDismiss()`类方法。然而，使用`onClick={this.onDismiss}`并不够，因为这个类方法需要接收`param`
属性来识别那个将要被忽略的项，这就是为什么它需要被封装到另一个函数中来传递这个属性。