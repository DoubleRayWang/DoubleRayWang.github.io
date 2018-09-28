---
layout: post
title: "学习 React"
date: 2018-09-01 13:13:05 +0800
categories: 前沿技术
tags: [JavaScript, ES6]
---

react众所周知的前端3大主流框架之一，由于出色的性能，完善的周边设施风头一时无两。本文就带大家一起掌握react。

**以下内容参考学习 [掌握react，这一篇就够了](https://segmentfault.com/a/1190000016281174)**
<!-- more -->

## jsx语法

前端MVVM主流框架都有一套自己的模板处理方法，react则使用它独特的jsx语法。在组件中插入html类似的语法，简化创建view的流程。

下面让我们来认识一下构建的两种元素

### 原生元素

```jsx
ReactDOM.render((
  <div>
    <h1>标题</h1>
  </div>
), document.getElementById('root'))
```

通过简单的语法页面就会被插入一个div+一个h1标签。原生的html元素可以被直接使用。以上的语法并不是js支持的语法，需要被转换之后才能运行。

### 自定义元素

react强大之处就在于可以组件的自定义，实现组件的复用。如果我们创建了一个组件。我们也可以通过jsx语法调用。

```jsx
import * as React from 'react'

class Page extends React.Component {
  render() {
    return (<div>
      home111 &copy; © \ua9
    </div>)
  }
}

ReactDOM.render((
  <div>
    <Page/>
  </div>
), document.getElementById('root'))
```

我们定义了一个Page组件，可以在jsx里面像调用html一样直接调用。

### 插入动态数据

```jsx
let name = 'hi'

ReactDOM.render((
  <div>
    {name}
  </div>
), document.getElementById('root'))
```

使用{}就可以插入数据，但是{}中间的必须是js表达式，不能是语句。如果表达式的执行结果是一个数组，则会自动join。

### 注释

jsx语法和html语法一样，也是可以插入注释，只不过写的时候有一些区别

#### 子组件注释


```jsx
let name = 'hi'

ReactDOM.render((
  <div>
    {/* 注释 */}
    {name}
  </div>
), document.getElementById('root'))
```

在子组件中插入注释，需要使用{}包裹起来，在/ /之间插入注释文字。

#### 属性注释


```jsx
let name = 'hi'

ReactDOM.render((
  <div>
    {name}
    <img /*
        多行注释
    */ src="1.jpg"/>
  </div>
), document.getElementById('root'))
```

在标签中间，可以插入一个多行注释，类似上面的代码。

### 属性props

* 可以向使用html的attr一样使用属性，就像下面img的src一样


```jsx
let name = 'hi'

ReactDOM.render((
  <div>
    <img src="1.png"/>
  </div>
), document.getElementById('root'))
```

* 如果需要传递动态属性，使用{}，多个属性，使用展开运算符


```jsx
let props = {
    src: '1.png',
    alt: '1图片'
}

ReactDOM.render((
  <div>
    <img src={"1.png"}/>
    <img {...props}/>
  </div>
), document.getElementById('root'))
```

* 两个转换,class-->className for-->htmlFor

因为class和for是javascript关键字，所以这里需要用转换之后名称

```jsx
ReactDOM.render((
  <div className="tab">
    <label htmlFor="name">姓名:</label><input id="name"/>
  </div>
), document.getElementById('root'))
```

* 布尔属性

如果一个属性的值是布尔值，当这个值是true的时候则可以省略=后面的值，只保留key。


```jsx
ReactDOM.render((
  <div className="tab">
    <input type="text" required/>
    <input type="text" required={true}/>
  </div>
), document.getElementById('root'))
```

* 原生元素的自定义属性

react对元素属性做了校验，如果在原生属性上使用此元素不支持的属性，则不能编译成功。必须使用data-前缀


```jsx
ReactDOM.render((
  <div className="tab">
    <input type="text" data-init="22"/>
  </div>
), document.getElementById('root'))
```

### 插入html

如果动态的插入html元素,react出于安全性考虑会自动帮我们转义。所以一定要动态的插入元素的话，使用dangerouslySetInnerHTML


```jsx
ReactDOM.render((
  <div className="tab">
    <div dangerouslySetInnerHTML={{__html: '<span>test</span>'}}></div>
  </div>
), document.getElementById('root'))
```

## React组件创建

### React.createClass

这是旧版本的api，使用React.createClass创建组件，配套的一些api，有getDefaultProps, getinitialstate。官方已经不建议使用了，使用下面新的api替代。

### ES6 classes

```jsx
import * as React from 'react'

class Page extends React.Component {
  render() {
    return (<div>
      home
    </div>)
  }
}
```

这是一个实现了render方法的class。也是一个基本的react组件。

### 无状态函数

```jsx
function Button(props, context) {
    return (
        <button>
            <em>{props.text}</em>
            <span>{context.name}</span>
        </button>
    );
}
```

纯函数,不存在state，只接受props和state。纯函数有优点，优点就是易于测试，无副作用。

## React数据流

### state

state是组件的内部状态，需要在视图里面用到的状态，才需要放到state里面去。如下，我们在类上创建一个state属性，在视图里面通过使用this.state.name去引用。而这里的state定义则代替的是getinitialstate方法。


```jsx
import * as React from 'react'

class Page extends React.Component {
  state = {
      name: '小明'
  }

  render() {
    return (<div>
      {this}
    </div>)
  }
}
```

如何更新state呢，直接更改state其实可以可以的，不过这样子无法触发组件视图的更新机制。所以使用setState()api。值得注意的是setState是异步的，原因是react内部需要对setState做优化，不是state变了立刻去更新视图，而是拦截一部分state的改变，等到合适的时机再去更新视图。


```jsx
import * as React from 'react'

class Page extends React.Component {
  state = {
      name: '小明'
  }

  render() {
    setTimeout(() => this.setState({name: '小明儿子'})， 5000)

    return (<div>
      {this.state.name}
    </div>)
  }
}
```

> **真实开发中绝不要在render函数里面去更改state，以上只是为了演示**

### props

props是组件之间传递数据的最主要api, react推崇的是自顶向下的数据流向，也就是组件的数据要从父组件传给子组件。如果子组件需要向父组件传递数据，则需要使用回调函数的方式。


```jsx
import * as React from 'react'

class Child extends React.Component {
  render() {
    return (<div>
      {this.props.parentName}
    </div>)
  }
}

class Parent extends React.Component {
  state = {
      name: '小明'
  }

  render() {
    setTimeout(() => this.setState({name: '小明儿子'})， 5000)

    return (<div>
      <Child parentName={this.state.name}/>
    </div>)
  }
}
```

可以看到Child组件显示了父组件的name。当父组件状态更新了，子组件同步更新。那如何在子组件中更改父组件状态呢？答案是回调函数。


```jsx
import * as React from 'react'

class Child extends React.Component {
  update() {
      this.props.onChange('小明名字改了')
  }

  render() {
    return (<div>
      {this.props.parentName}
      <button onClick={this.update.bind(this)}>更新</button>
    </div>)
  }
}

class Parent extends React.Component {
  state = {
      name: '小明'
  }

  changeName(name) {
      this.setState({
          name
      })
  }

  render() {
    setTimeout(() => this.setState({name: '小明儿子'})， 5000)

    return (<div>
      <Child onChange={this.changeName.bind(this)} parentName={this.state.name}/>
    </div>)
  }
}
```

> **注意哈：props是不可以更改的，这既不符合react单向数据流思想，也为维护带来灾难**。

### 事件

react里面的用户事件都是合成事件，被React封装过。内部使用的还是事件的委托机制。
常用的事件有点击事件onClick，input的onChange事件等，官网都可以查到。

#### 合成事件的this指向问题

就像上文一样，我们绑定事件的方式很奇怪，使用了bind来显示绑定this的指向。因为传递到组件内部的只是一个函数，而脱离了当前对象的函数的this指向是不能指到当前组件的，需要显示指定。

* 通过bind


```jsx
<button onClick={this.update.bind(this)}>更新</button>
```

* 构造器内部指定


```jsx
import * as React from 'react'

class Child extends React.Component {
  constructor(props) {
     super(props)
     this.update = this.update.bind(this)
  }

  update() {
      this.props.onChange('小明名字改了')
  }

  render() {
    return (<div>
      {this.props.parentName}
      <button onClick={this.update}>更新</button>
    </div>)
  }
}
```

* 箭头函数


```jsx
import * as React from 'react'

class Child extends React.Component {
  update => e = {
      this.props.onChange('小明名字改了')
  }

  render() {
    return (<div>
      {this.props.parentName}
      <button onClick={this.update}>更新</button>
    </div>)
  }
}
```

* 装饰器


```jsx
import * as React from 'react'

class Child extends React.Component {
  constructor(props) {
     super(props)
  }

  @autoBind
  update() {
      this.props.onChange('小明名字改了')
  }

  render() {
    return (<div>
      {this.props.parentName}
      <button onClick={this.update}>更新</button>
    </div>)
  }
}
```
装饰器是es7语法，如果需要使用需要安装对应的babel：present版本。而typescript则原生支持。

> **autoBind原理大概就是劫持get方法，get时改变this指向**

#### 如何获得evnt原生事件

通过e.nativeEvent获取原生事件对象


```jsx
import * as React from 'react'

class Child extends React.Component {
  constructor(props) {
     super(props)
     this.update = this.update.bind(this)
  }

  update(e) {
      console.log(e.nativeEvent)
  }

  render() {
    return (<div>
      <button onClick={this.update}>更新</button>
    </div>)
  }
}
```

#### 解决冒泡和取消默认事件

```js
e.preventDefault() //取消默认行为
```

```js
e.stopPropagation() //取消冒泡
```

这个和浏览器原生事件处理方案是一致的。问题是我们只可以调合成事件的e的方法，不可以通过e.nativeEvent方法做这些操作，原因是上文讲过的委托。

## ReactDom

### ref

特殊的props，ref组件对象的引用，现在官方也不建议直接给ref赋值，需要通过函数来赋值。


```jsx
ReactDOM.render((
  <div>
    <Calendar ref={ref => this.c = ref} any-ss="text"/>
  </div>
), document.getElementById('root'))
```

### render

顶层api,只有在根组件时候才需要使用。第一个参数是Component,第二个参数是dom节点

### findDOMNode

通过传入component实例获取此component根dom节点，在这里可以去dom节点进行操作了，虽然极其不建议这么做，但是你确实可以做。

### unmountComponentAtNode

卸载此组件，并销毁组件state和事件。
接收组件的引用，也就是ref。仅仅是取消挂载，组件还在，如果需要彻底清除的话，需要手动删掉此dom。

## 表单

### onchange配合value

与vue框架不同的是，react如果要实现表单元素变化，状态同步更新，必须要自己去监听表单事件。


```jsx
import * as React from 'react'

class Child extends React.Component {
  state = {
      name: '小明'
  }

  constructor(props) {
     super(props)
     this.update = this.update.bind(this)
  }

  update(e) {
      this.setState({
          name: e.target.value
      })
  }

  render() {
    return (<div>
      <input onChange={this.update} value={this.state.name}/>
    </div>)
  }
}
```

### 受控组件和非受控组件

受控组件和非受控组件这些都是指的表单组件，当一个表单的值是通过value改变的而不是通过defaultValue是受控组件，否则就是非受控组件。

* 下面组件中的input就是受控组件


```jsx
import * as React from 'react'

class Child extends React.Component {
  state = {
      name: '小明'
  }

  constructor(props) {
     super(props)
     this.update = this.update.bind(this)
  }

  update(e) {
      this.setState({
          name: e.target.value
      })
  }

  render() {
    return (<div>
      <input onChange={this.update} value={this.state.name}/>
    </div>)
  }
}
```

* 下面组件中的input是非受控组件


```jsx
import * as React from 'react'

class Child extends React.Component {
  state = {
      name: '小明'
  }

  constructor(props) {
     super(props)
     this.update = this.update.bind(this)
  }

  update(e) {
      this.setState({
          name: e.target.value
      })
  }

  render() {
    return (<div>
      <input onChange={this.update} defaultValue={this.state.name}/>
    </div>)
  }
}
```

## 组件之间通讯

### 父子之间通讯

父子之间通讯又分为父->子，子->父。

因为react单向数据流向的缘故，父->子通信的话直接通过props。父组件数据变动，直接传递给子组件。

子->父组件之间就要通过回调函数来通信了，父组件传递一个回调函数给子组件，子组件通过调用此函数的方式通知父组件通信。

### 跨级组件通信

react为了实现祖先组件和后辈组件之间的通信问题，引入了contextApi。


```jsx
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.context.color}}>
        {this.props.children}
      </button>
    );
  }
}

Button.contextTypes = {
  color: React.PropTypes.string
};

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  getChildContext() {
    return {color: "purple"};
  }

  render() {
    const children = this.props.messages.map((message) =>
      <Message text={message.text} />
    );
    return <div>{children}</div>;
  }
}

MessageList.childContextTypes = {
  color: React.PropTypes.string
};
```

MessageList中的color会自动更新到儿孙组件里面去，实现跨级啊通信。如果需要反过来通信，则需要借助其他工具，比如事件系统(Pub/Sub)。

### 没有嵌套关系组件之间通信

组件之间通信最主流的两种方式脱胎于观察这模式和中介者模式这两种。

跨级之间通信现在最主流的方式就是观察这模式的实现Pub/Sub，react社区中的redux也是使用这种方式实现的。

*vue2.X版本也去掉了跨组件通信的功能。那如何在2.x中做跨组件通信呢？如果不借助外力的话，是不是可以使用$parent和$childen的递归调用实现全局组件通信呢？比如我想广播一个事件，我就查找到所有的子组件，挨个触发$emit(xx)，上报一个事件也是同理，只不过需要查找所有的$parent。结合起来就可以实现组件之间的通信，只不过这种查找效率比较低，需要慎用和优化*