---
layout: post
title: "React学习总结"
date: 2018-11-17 18:13:05 +0800
categories: 干货分享
tags: [JavaScript,React]
---

## React开发步骤总结

1. UI拆分（单一职责原则）
2. 构建静态版本（无state，无交互，可测试页面构成）
3. 确定state（为了好维护：最小但完整）
4. 某些可以根据props计算而来 或者 根据已有state和props计算而来的 不是必须state。
5. 确定状态的位置
6. 考虑涉及到同一state的组件有哪些，将该state放到所有关联组件的共同祖先组件
7. 反向数据流
8. 在确定好state之后，通过props向下传递，形成正向数据流；反向数据流就是事件响应等修改祖先组件中的state。

## React基础知识总结

1. React只更新必需要更新的部分，即使render了整个react元素，也只更新需要更新的部分，不必纠结render执行的资源消耗，参见[react diff](https://zh-hans.reactjs.org/docs/reconciliation.html#the-diffing-algorithm)
2. JSX 可防止注入攻击，由于其在渲染前，所有数据会被转义成字符串处理；JSX本身可看作对象，即React元素，React会根据这些React元素来构建真正的DOM，并保持更新。
3. state状态更新可能是异步的，React 为了优化性能，有可能会将多个 setState() 调用合并为一次更新，所以不能依赖他们的值计算下一个state(状态)；
4. 组件的状态只能自身访问，组件外不能获取；一个组件可以选择将 state(状态) 向下传递，作为其子组件的 props(属性)；
5. **事件处理** (通过preventDefault来阻止默认行为)，注意事件处理函数的this绑定；
6. **条件渲染：**
    - if + 变量，根据不同state或者props来返回不同组件；
    - 在返回的组件JSX中使用 && ，JSX中可使用任何表达式（内联）;
    - 三元表达式（内联）；
7. 组件属性 props.children 表示组件包裹的元素，可包裹JSX组件元素、字符串、{}包裹的js表达式、回调函数。false，null，undefined，和 true 都是有效的的 children(子元素)，但是并不会被渲染。
8. return null 可防止组件被渲染（可在函数组件中return null），从组件的 render 方法返回 null 不会影响组件生命周期方法的触发。 例如， componentWillUpdate 和 componentDidUpdate 仍将被调用；
9. **列表渲染：**
    - 列表组件
    - 键(Keys) 帮助 React 标识哪个项被修改、添加或者移除了。数组中的每一个元素都应该有一个唯一不变的键(Keys)来标识；keys 在同辈元素中必须是唯一的，不需要全局唯一；key不会作为props传入组件内部，若想使用，可利用props传入相同的值；
10. **表单**（受控组件）(React元素中input、textare、select标签的value，onChange与selected属性都与HTML元素不同，都接受一个 value 属性来实现一个受控组件)
11. **状态提升：**
    - React遵循单一数据流原则，即从上到下的数据流，通过将state转props进行父子通信，若需要组件间通信，则需要将组件的状态提升到最近的共同祖先组件；
    - 当你看到 UI 中的错误，你可以使用 React 开发者工具来检查 props ，并向上遍历树，直到找到负责更新状态的组件。这使你可以跟踪到 bug 的源头;
    - 组件可以接受任意的 props(属性) ，包括原始值、React 元素，或者函数;
    
## redux 学习记录

[中文文档 segmentfault](https://segmentfault.com/a/1190000017064759)

[中文文档 GitBook](https://cn.redux.js.org/)

### redux 的来源与思想

1. 来源：Redux 由 Flux 演变而来，但受 Elm （函数式编程语言）的启发，避开了 Flux 的复杂性。
2. 思想：Web 应用是一个状态机，视图与状态是一一对应的。所有的状态，保存在一个对象里面。让 state 的变化变得可预测 。
3. 三大原则：
    - 单一数据源（整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中）
    - State 是只读的（唯一改变 state 的方法就是触发 action）
    - 使用纯函数来执行修改（通过 reducers 函数来修改 state tree）
    
### redux 的适用场景

从应用的角度：

+ 用户的使用方式复杂
+ 不同身份的用户有不同的使用方式（比如普通用户和管理员）
+ 多个用户之间可以协作
+ 与服务器大量交互，或者使用了WebSocket
+ View要从多个来源获取数据

从组件的角度：

+ 某个组件的状态，需要共享
+ 某个状态需要在任何地方都可以拿到
+ 一个组件需要改变全局状态
+ 一个组件需要改变另一个组件的状态
