---
title: jsx
date: 2021-05-19 11:21:48
tags:
 - 前端
 - JAVASCRIPT
categories:
 - 前端
 - JAVASCRIPT
---

## JSX

JSX 是一个静态类型、面向对象的编程语言，主要设计用来在浏览器上运行，你可以把它当作是 JavaScript 的增强版本。

## 使用

```jsx
const list = []
const flag = false
function handleClick () {
    console.log('this is the click method')
}
function paramClick (obj = {}) {
    console.log (`param is ${JSON.stringfy(obj)}`)
}
function render () {
    return (
        <div>
            <a onClick={handleClick} title={title}>绑定事件和变量</a>
            <a onClick={() => paramClick(params)} title={title}>带参事件</a>
            {
                list.map((item, index) => (
                    <div key={index} {...item}>循环显示</div>
                ))
            }
            {
                flag && <div class='flag-ele'>条件判断显示</div>
            }
        </div>
    )
}
```

目前  vue-cli3 和 React 已经很好地支持JSX语法了，不需要安装额外的插件来配置
