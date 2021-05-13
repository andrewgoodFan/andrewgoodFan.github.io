---
title: React项目
date: 2021-04-22 17:31:21
tags:
- 前端
- React
categories:
- 前端
- React
---

## 技术选型

1. 基于React的umi框架
2. React Hooks
3. Ant Design
4. dva

## 框架介绍

 React Hook 采用函数式组件，更轻量快速，更易于组件化的封装和通用，细化组件，Hook 使你在无需修改组件结构的情况下复用状态逻辑
 umi 是可扩展的企业级前端应用框架。Umi 以路由为基础的，同时支持配置式路由和约定式路由，保证路由的功能完备，并以此进行功能扩展。然后配以生命周期完善的插件体系，覆盖从源码到构建产物的每个生命周期，支持各种功能扩展和业务需求。
 antd 也是一套成熟的ui，对于PC端的项目， 很适用

###  umi搭建项目

1. 脚手架搭建 （详见官网）
2. 路由 

````
const routes = [
    {
        path: '',
        component: '',
        routes: [
            {
                path: '',
                component: ''
            }
        ]
    }
]
export default routes
````
3. 基础配置
````
基础配置
export default {
    // 别名
    alias: {
        foo: '/tmp/a/b/foo',
    },
    // 设置路由前缀，通常用于部署到非根目录。
    base: '/',
    // 用于提供给代码中可用的变量
    define: {
        BASE_API: ''
    },
    // 设置哪些模块可以不被打包，通过 <script> 或其他方式引入，通常需要和 scripts 或 headScripts 配置同时使用。
    externals: {
        别名: 'window.别名',
    },
    scripts: [
        'cdn地址',
    ],
    // 如果要使用本地的图片，图片请放到 public 目录
    favicon: '',
    metas:[
        {
        name: 'keywords',
        content: '',
        },
        {
        name: 'description',
        content: '',
        },
        {
        bar: '',
        },
    ],
    // 配置代理能力
    proxy: {},
    // 配置路由
    routes, 
};
````

4. 运行时配置

````
// app.tsx

1. 请求配置 注册到  umi-request
export const request: RequestConfig = {
    requestInterceptors: [], // 请求拦截
    responseInterceptors: [] // 响应拦截
};

2. 动态路由匹配

let extraRoutes;

export function patchRoutes({ routes }) {
  merge(routes, extraRoutes);
}

export function render(oldRender) {
  fetch('/api/routes').then(res=>res.json()).then((res) => { 
    extraRoutes = res.routes;
    oldRender();
  })
}


3. 初始化值

export async function getInitialState() {
  const data = await fetchXXX();
  return data;
}

在组件中通过 useModel('@@initialState') 可获取初始值

useModel： umi 内置自定义hook,可获取初始化和设置初始值
````

### 使用dva进行状态管理

#### 约定式的 model 组织方式
1. src/models 下的文件
2. src/pages 下，子目录中 models 目录下的文件
3. src/pages 下，所有 model.ts 文件(不区分任何字母大小写)

#### 配置

```ts
import { Effect, ImmerReducer, Reducer, Subscription } from 'umi';

export interface IndexModelState {
  name: string;
}

export interface IndexModelType {
  namespace: 'index';
  state: IndexModelState;
  effects: {
    act: Effect;
  };
  reducers: {
    add: Reducer<IndexModelState>;
    // 启用 immer 之后
    // save: ImmerReducer<IndexModelState>;
  };
  subscriptions: { setup: Subscription };
}

const IndexModel: IndexModelType = {
  namespace: 'IndexModel',
  // 初始数据
  state: {
    name: '',
  },
//   同步方法 修改state reducer
  effects: {
    *act({ payload }, { call, put }) {},
  },
//   异步方法 actions
  reducers: {
    add(state, action) {
      return {
        ...state,
        ...action.payload,
      };
    },
    // 启用 immer 之后
    // add(state, action) {
    //   state.name = action.payload;
    // },
  },
//   进入页面 路由事件
  subscriptions: {
    setup({ dispatch, history }) {
      return history.listen(({ pathname }) => {
      });
    },
  },
};

export default IndexModel;
```

#### 使用

```jsx
import React from 'react';
import {connect, Loading } from 'umi';

export default connect(
  ({ IndexModel, loading }: { IndexModel: IndexModelState; loading: Loading }) => ({
    IndexModel,
    loading: ['index/act'],
  }),
)(IndexPage);
```