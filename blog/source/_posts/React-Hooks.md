---
title: React Hooks
date: 2021-05-07 15:58:40
tags:
- 前端
- React
categories:
- 前端
- React
---

## React Hooks

函数组件中也可以实现状态管理

Hook 是一个特殊的函数，它可以让你“钩入” React 的特性

Hook 使你在无需修改组件结构的情况下复用状态逻辑

### Hook 规则

1. 只在最顶层使用 Hook
2. 只在 React 函数中调用 Hook

### useState/useReducer

useState 和 useReducer 都是关于状态值的提取和更新，从本质上来说没有区别，从实现上看，useState 是 useReducer 的一个简化版，其背后用的是同一套逻辑。

```jsx
const DemoPage: React.FC = (props) => {
    const [count, setCount] = usestate<number>(0)
    render () (
        <div onClick={() => setCount( count += 1)}>
            {count}
        </div>
    )
}
```

### useEffect/useLayoutEffect 

可用作生命周期函数

```jsx
const DemoPage: React.FC = (props) => {
    const [count, setCount] = usestate<number>(0)
    useEffect(() => {
        // do something

        return () => {
            // 清除副作用
        }
    }, [
        // 依赖数组，监听数据变化
    ])
    render () (
        <div onClick={() => setCount( count += 1)}>
            {count}
        </div>
    )
}
 
```

### useMemo

计算属性， 根据依赖值的变化响应式改变

### useCallback

useCallback其实是利用memoize减少不必要的子组件重新渲染

### useRef/useImperativeHandle

获取子组件实例 子组件返回数据给父组件

### useContext/createContext

创建共享状态

```jsx
const ParentContext = createContext({})

const Child: React.FC = (props) => {
    const {username} = useContext(ParentContext)
    return (
        <div>{username}</div>
    )
}
const DemoPage: React.FC = (props) => {
    const [count, setCount] = usestate<number>(0)
    useEffect(() => {
        // do something

        return () => {
            // 清除副作用
        }
    }, [
        // 依赖数组，监听数据变化
    ])
    return (
        <ParentContext.Provider
        value={{username: '1111'}}
        onClick={() => setCount( count += 1)}>
            <Child />
        </ParentContext.Provider>
    )
}

```

### 自定义 Hook

1. 自定义 Hook 是一个函数，其名称以 “use” 开头，函数内部可以调用其他的 Hook
2. 自定义 Hook 必须以 “use” 开头
3. 在两个组件中使用相同的 Hook 不会共享 state 
