---
title: Vue3 - TypeScript - Vite
date: 2021-05-20 16:05:58
tags:
 - 前端
 - Vue
categories:
 - 前端
 - Vue
---

## 项目初始化

1. `npm init @vitejs/app`
```ts
{
    Project name 'vue3_demo',
    framework: 'vue',
    variant: 'TypeScript'
}
```

2. `cd vue3_demo` -> `npm i` -> `npm run dev`


## vue3知识

### ref和reactive的区别
ref 的作用就是将一个原始数据类型（primitive data type）转换成一个带有响应式特性的数据类型

reactive是用来定义更加复杂的数据类型，但是定义后里面的变量取出来就不在是响应式Ref对象数据了,所以需要用toRefs函数转化为响应式数据对象

reactive 和 ref 都是用来定义响应式数据的 reactive更推荐去定义复杂的数据类型 ref 更推荐定义基本类型

### setup

```ts
import { toRefs } from 'vue'
setup(props, context) {
    /**
     * props 
     * 响应式的，你不能使用 ES6 解构，因为它会消除 prop 的响应性。
     * 如果需要解构 prop，可以通过使用 setup 函数中的 toRefs
     * 
    */
   props.title ? (const { title } = toRefs(props)) : ( const title = toRef(props, 'title'))
   /**
    * context
   */
    const {attr,slot, emit} = context

    /**
     * 返回值
     * obj 可供模板使用的
     * render函数  
    */
    return obj  || () => VNode
  }
```

### hook

hook方法需要定义在 setup 顶层

### vue动态组件

```ts
<component :is="currentTabComponent"></component>
```

### JSX使用插槽

```ts
render() {
    console.log('sidemenu', this.route)
    console.log('sidemenu', this.menu)
    const slots = {
        title: () => <div>
            {this.itemMenu?.meta?.icon && <i class={this.itemMenu.meta.icon}></i> || null}
            <span>{this.itemMenu.meta?.title || '默认菜单'}</span>
        </div>
    }
    return (
        <el-submenu v-slots={slots} index={this.itemMenu.path}>
            {
                this.itemMenu.children?.map((item) => {
                    if (item.children && item.children.length) {
                        return <SideMenuItem itemMenu={item} />
                    } else {
                        return <el-menu-item index={item.path}>{item.meta?.title || '默认菜单'}</el-menu-item>
                    }
                })
            }
        </el-submenu>
    )
},
```

### Vue3响应式
`Proxy` - 代理，顾名思义，就是在要访问的对象之前增加一个中间层，这样就不直接访问对象，而是通过中间层做一个中转，通过操作代理对象，来实现修改目标对象
`reactive`创建响应式代理对象
```js
    function createReactiveObject(
    target: unknown,
    toProxy: WeakMap<any, any>,
    toRaw: WeakMap<any, any>,
    baseHandlers: ProxyHandler<any>,
    collectionHandlers: ProxyHandler<any>
    ) {
    // 如果不是对象，直接返回，开发环境下会给警告
    if (!isObject(target)) {
        if (__DEV__) {
        console.warn(`value cannot be made reactive: ${String(target)}`)
        }
        return target
    }
    // 目标对象已经是可观察的，直接返回已创建的响应式Proxy，toProxy就是rawToReactive这个WeakMap，用于映射响应式Proxy
    let observed = toProxy.get(target)
    if (observed !== void 0) {
        return observed
    }
    // 目标对象已经是响应式Proxy，直接返回响应式Proxy，toRaw就是reactiveToRaw这个WeakMap，用于映射原始对象
    if (toRaw.has(target)) {
        return target
    }
    // 目标对象是不可观察的，直接返回目标对象
    if (!canObserve(target)) {
        return target
    }
    // 下面是创建响应式代理的核心逻辑
    // Set、Map、WeakMap、WeakSet的响应式对象handler与Object和Array的响应式对象handler不同
    const handlers = collectionTypes.has(target.constructor)
        ? collectionHandlers
        : baseHandlers
    // 创建Proxy
    observed = new Proxy(target, handlers)
    // 更新rawToReactive和reactiveToRaw映射
    toProxy.set(target, observed)
    toRaw.set(observed, target)
    // 看reactive的源码，targetMap的用处目前还不清楚，应该是作者预留的尚未完善的feature而准备的
    if (!targetMap.has(target)) {
        targetMap.set(target, new Map())
    }
    return observed
    }
    export function reactive(target: object) {
    // 如果是readonly对象的代理，那么这个对象是不可观察的，直接返回readonly对象的代理
    if (readonlyToRaw.has(target)) {
        return target
    }
    // 如果是readonly原始对象，那么这个对象也是不可观察的，直接返回readonly对象的代理，这里使用readonly调用，可以拿到readonly对象的代理
    if (readonlyValues.has(target)) {
        return readonly(target)
    }

    // 调用createReactiveObject创建reactive对象
    return createReactiveObject(
        target, // 目标对象
        rawToReactive, // 原始对象映射响应式对象的WeakMap
        reactiveToRaw, // 响应式对象映射原始对象的WeakMap
        mutableHandlers, // 响应式数据的代理handler，一般是Object和Array
        mutableCollectionHandlers // 响应式集合的代理handler，一般是Set、Map、WeakMap、WeakSet
    )
}
```




### vetur 无法识别模块 (强迫症解决)
1. 在vscode中添加插件：`Path Intellisense`
2.  vscode设置 
```js
"path-intellisense.mappings": {
        "@": "${workspaceRoot}/src"
    },
```
3. 项目设置

```ts
// tsconfig.ts
"paths": {
    "@/*": ["./src/*"]
}
```

### 搭建项目框架

`vue3 + vite + typescript + elementplus`

1.  第一个页面

```ts
// login.vue

<template>
    <el-form
        :model="ruleForm"
        status-icon
        :rules="rules"
        ref="refRuleForm"
        label-width="100px"
    >
        <el-form-item label="账号" prop="loginAccount">
            <el-input
                type="text"
                v-model="ruleForm.loginAccount"
                autocomplete="off"
            ></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="loginPassword">
            <el-input
                type="password"
                autocomplete="off"
                v-model="ruleForm.loginPassword"
            ></el-input>
        </el-form-item>
        <el-form-item>
            <el-button type="primary"
            @click="submit(ruleForm)"
                >登录</el-button
            >
        </el-form-item>
    </el-form>
</template>

<script lang="ts">
import { defineComponent, onMounted, reactive, toRef, toRefs } from 'vue'
import {useRouter} from 'vue-router'
import {loginService} from '../api/login'
import {enCodeMd5, saveLoginInfo} from '@/utils/utils'
import {CommonTypeProps} from '../types/common'
import { AxiosResponse } from 'axios'

interface RuleFormProps {
    loginAccount: string
    loginPassword: string

}
export default defineComponent({
    setup () {
        const router = useRouter()
        const rules: any = {
            loginAccount: [{
                required: true,
                message: '请输入手机号'
            }],
            loginPassword: [{
                required: true,
                message: '请输入手机号'
            }]
        }
        const ruleForm: RuleFormProps = reactive({
            loginAccount: '',
            loginPassword: ''
        })
        const refRuleForm = toRefs(ruleForm)
        const submit = async (obj: any) => {
            console.log(obj)
            try {
                const res:AxiosResponse<CommonTypeProps.ResponseProps> = await loginService({
                    ...obj,
                    loginPassword: enCodeMd5(obj.loginPassword)
                })
                console.log('res', res)
                if (res?.data?.bizCode === 'SUCCESS') {
                    console.log(res.data.respData)
                    saveLoginInfo(res.data.respData)
                    
                    router.push('/')
                }
            } catch (error) {
                
            }
        }
        return {
            ...refRuleForm,
            ruleForm,
            rules,
            submit
        }
    }
})
</script>

```

2. 路由配置

```ts
//router/route.ts
import {RouteRecordRaw} from 'vue-router'

const routes: RouteRecordRaw[] = [
    {
        path: '/login',
        component: () => import('@/views/login.vue')
    },
    {
        path: '/',
        component: () => import('@/views/layout.vue'),
        redirect: '/index',
        meta: {
            name: '首页'
        },
        children: [
            {
                path: 'index',
                component: () => import('@/views/index/index.vue'),
                meta: {
                    name: '首页'
                }
            },
            {
                path: 'order',
                component: () => import('@/views/index/index.vue'),
                meta: {
                    name: '订单'
                }
            },
            {
                path: 'goods',
                component: () => import('@/views/index/index.vue'),
                meta: {
                    name: '商品'
                }
            }
        ]
    }
]
export default routes
// router/index.ts
import {createRouter, createWebHashHistory} from 'vue-router'
import routes from './route'

const router = createRouter({
    history: createWebHashHistory(),
    routes: routes
})

export default router
```

3. 动态路由配置

```js
// Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块
const modules = import.meta.glob('../views/**/**.vue')
获取页面文件信息与后台返回的菜单数组匹配，获取路由数组，使用 `addRoute` 方法动态导入到路由中
getMenuDataService().then(res => {
    if (res.data.bizCode === 'SUCCESS') {
        const treeData = listToTree(res.data.result)
        const routerList = addRouteRedirect(treeData)
        const layout = routes.find((item) => item.name === 'layout')!
        layout.children = [...layout.children!, ...routerList]
        router.addRoute(layout)
        router.addRoute({
            path: '/404',
            component: () => import('@/views/404.vue')
        })
        resolve(layout.children)
    }

})
.catch((err) => {
    reject(err)
})
```