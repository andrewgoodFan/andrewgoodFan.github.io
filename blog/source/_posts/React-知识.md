---
title: React 知识
date: 2021-05-07 15:13:28
tags:
- 前端
- React
categories:
- 前端
- React
---

## React 知识小记

### 认识React

React 是一个声明式，高效且灵活的用于构建用户界面的 JavaScript 库。使用 React 可以将一些简短、独立的代码片段组合成复杂的 UI 界面，这些代码片段被称作“组件”。

### 基础知识

#### react中的受控组件和非受控组件的实现方式

受控组件：将state里面的数据绑定给表单元素，给表单元素注册onChange事件，通过onChange将表单元素的值同步给state

非受控组件：通过创建ref，从而获取到表单元素，通过表单元素的value方式获取到输入框的值

#### react中实现组件通讯的方式

父传子：通过属性绑定的形式进行传递，子组件通过props形式进行接收

子传父：实际上是通过父组件给子组件传了一个回调函数，子组件通过调用父组件的回调函数，通过传参的形式进行传递

兄弟组件：将传递的数据和修改数据的方法，提到就近的父组件上面，通过父组件传值，传递数据和方法，传递给对应的兄弟组件

跨组件传值：创建一个context，通过provider和consumer分别进行传值和接收值

#### Render Props
在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术
具有 render prop 的组件接受一个函数，该函数返回一个 React 元素并调用它而不是实现自己的渲染逻辑。
```
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```

#### 高阶组件

也是为了复用组件的状态和操作状态的逻辑

封装一个高阶组件：封装一个高阶组件实际就是封装一个函数，函数内部提供一个组件，用来复用状态和操作状态的逻辑，至于显示的ui结构，通过函数调用传递一个ui组件，将传递的ui组件进行显示，并且把数据暴漏给这个ui组件。最终这个函数返回一个新的组件

#### 虚拟DOM

1.  利用 JavaScript 创建 DOM 树
2.  树的 diff，同层对比，输出 patchs(listDiff / diffChildren / diffProps)
    1.  没有新的节点，返回
    2.  新的节点 tagName 与 key 不变，对比 props，继续递归遍历子树
        1.  对比属性（对比新旧属性列表）
        2.  都存在的是否有变化
        3.  是否出现旧列表中没有的新属性
    3.  tagName 和 key 值变化了，则直接替换成新节点
3.  渲染差异
    1.  遍历 patchs，把需要更改的节点取出来
    2.  局部更新 DOM

````
const virtual = {
    type: 'h1',
    props: {
        className: '',
        children: '111'
    }
}

````

#### React 中 key 的作用

```html
<!-- 更新前 -->
<div>
  <p key="ka">ka</p>
  <h3 key="song">song</he>
</div>

<!-- 更新后 -->
<div>
  <h3 key="song">song</h3>
  <p key="ka">ka</p>
</div>
```

如果没有 key，React 会认为 div 的第一个子节点由 p 变成 h3，第二个子节点由 h3 变成 p，则会销毁这两个节点并重新构造。

但是当我们用 key 指明了节点前后对应关系后，React 知道 `key === "ka"` 的 p 更新后还在，所以可以复用该节点，只需要交换顺序。

key 是 React 用来追踪哪些列表元素被修改、被添加或者被移除的辅助标志。

在开发过程中，我们需要保证某个元素的 key 在其同级元素中具有唯一性。在 React diff 算法中，React 会借助元素的 Key 值来判断该元素是新近创建的还是被移动而来的元素，从而减少不必要的元素重新渲染。同时，React 还需要借助 key 来判断元素与本地状态的关联关系。


#### React 中 ref 的作用

ref 是 React 提供的一种可以安全访问 DOM 元素或者某个组件实例的方式。

在类组件中使用 `createRef()`，在函数组件中使用 `useRef` 。

#### JSX

JSX是一个 JavaScript 的语法扩展。我们建议在 React 中配合使用 JSX，JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式。JSX 可能会使人联想到模版语言，但它具有 JavaScript 的全部功能。