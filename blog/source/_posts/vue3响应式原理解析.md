---
title: vue3响应式原理解析
date: 2021-08-17 11:24:04
tags:
 - 前端
 - Vue
categories:
 - 前端
 - Vue
---

## Vue3响应式方案

Vue3中使用了ES6提供的`Proxy`来实现对数据的侦测.
  - 可以监听属性的增删操作。
  - 可以监听数组某个索引值的变化以及数组长度的变化

### Proxy

`Proxy` 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。`Proxy` 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

具体知识可参考阮一峰老师的[《ES6 入门教程》](https://es6.ruanyifeng.com/#docs/proxy)

```ts
  const proxy = new Proxy(target: object, handler: object)
  const testProxy = new Proxy({}, {
    get(target: object, key: string, receiver: Proxy) {
      return Reflect.get(target, key, receiver)
    },
    set(target: object, key: string, value: any, receiver: Proxy) {
      return Reflect.set(target: object, key: string, value: any, receiver: Proxy)
    }
  })
  testProxy.a = 1 // 
  testProxy.a
```

### Reflect

`Reflect`对象与`Proxy`对象一样，也是 ES6 为了操作对象而提供的新 API
可以用来改写对象的某些方法，优化返回值

```ts
  const deeps = new Map()
  const observe = (key, fn) => deeps.add(key, fn)
  const observable = obj => new Proxy(obj, {get, set})
  function get(target, key, receiver) {
    return Reflect.get(target, key, receiver)
  }
  function set(target, key, value, receiver) {
    const result = Reflect.set(target, key, value, receiver)
    deeps.forEach((deep, deepKey) => {
      deepKey === key && deep()
    })
    return result
  }
```

### Set and WeakSet

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值

```ts
  const set = new Set([1, 2, 3])
  set.add(4)
  set.delete(3)
  set.has(2)
  set.clear()
  set.values()
  set.keys()
  set.entries()
  set.forEach()

  /* ============== **/
  // WeakSet 的成员只能是对象，而不能是其他类型的值
  // WeakSet 中的对象都是弱引用, 不可遍历
  // 用来储存数据 判断数据是否存在
  const ws = new WeakSet()
  ws.add([1, 2])
  ws.clear([1, 2])
  ws.has([1, 2])
```

### Map and WeakMap

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构

```ts

  const map = new Map()
  const key = {p: '111'}
  map.add(key, 'value')
  map.get(key) // 'value'
  map.has(key) // true
  map.delete(key)
  map.has(key) // false
  map.clear()
```
### Vue3响应式原理

* 通过 effect  声明依赖响应式数据的函数cb ( 例如视图渲染函数render函数)，并执行cb函数，执行过程中，会触发响应式数据 getter

* 在响应式数据 getter中进行 track依赖收集：建立 数据&cb 的映射关系存储于 targetMap

* 当变更响应式数据时，触发 trigger **，**根据 targetMap 找到关联的cb执行

#### Vue3响应式实现

```ts
  // 建立响应式数据
  function reactive(obj){}
  // 声明响应函数cb(依赖响应式数据)
  function effect(cb){}
  // 依赖收集
  function track(target, key){}
  // 触发更新
  function trigger(target, key){}

  function isObject (val) {
    return val !== null && typeof val === 'object'
  }

  function reactive(obj) {
    if (!isObject(obj))return obj
    const observed = new Proxy(obj, { 
      get(target, key, receiver) {
        const result = Reflect.get(target, key, receiver)
        console.log("getter", result)
        track(target, key)
        return reactive(result)
      },
      set(target, key, value, receiver) {
        const result = Reflect.set(target, key, value, receiver)
        console.log('setter', result)
        trigger(target, key)
        return result
      }
    })
    return observed
  }

  const effectStack = []

  function effect(cb) {
    const _effect = function () {
      try {
        console.log(_effect)
        effectStack.push(_effect)
        return cb()
      } finally {
        console.log('finally')
        effectStack.pop()
      }
    }
    _effect()
    console.log(effectStack)
    return _effect
  }

  const targetMap = new WeakMap()
  function track(target, key) {
    const effectFn = effectStack[effectStack.length - 1]
    if (effectFn) {
      let depsMap = targetMap.get(target)
      if (!depsMap) {
        depsMap = new Map()
        targetMap.set(target, depsMap)
      }
      let deps = depsMap.get(key)
      if (!deps) {
        deps = new Set()
        depsMap.set(key, deps)
      }
      deps.add(effectFn)
    }
  }

  function trigger(target, key) {
    const depsMap = targetMap.get(target)
    if (depsMap) {
      const deps = depsMap.get(key)
      if (deps) {
        deps.forEach(effect => effect())
      }
    }
  }
```