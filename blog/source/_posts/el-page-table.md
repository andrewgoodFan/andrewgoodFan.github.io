---
title: el-page-table
date: 2021-05-19 11:18:54
tags:
 - 前端
 - components
 - Vue
categories:
 - 前端
 - components
 - Vue
---


# el-page-table
基于 el 的页面级列表插件

## 参数

|  参数名 | 说明  | 类型  | 可选值 | 默认值 |
| ------ | ----- | ----- | ----- | ------ |
| columns | 列数组 | array | — | [] |
| dataSource | 显示的数据 | array | — | [] |
| total | 列表总数 | number | — | 0 |
| currentPage | 列表当前页数 | number | — | 1 |
| loading | 列表加载中 | boolean | — | — |
| searchData | 请求参数 | object, undefined | — | — |
| query | 列表请求方法 | Function, undefined | — | — |
| querySuccess | 请求成功之后的回调 | Function, undefined | — | — |
| showPagination | 是否显示分页 | boolean | — | true |
| type | 对应列的类型 | string | selection/index/expand | — |
| rowSelection | 多选框参数集合, 设置了`type=selection`,可以设置多选框选中事件 | Object, undefined | — | — |
| formProps | 搜索表单参数 （自定义布局） | Object, undefined | — | — |
| middleSlot | 列表头部左侧VNode | VNode, undefined | — | — |
| middleButton | 列表头部右侧VNode | VNode, undefined | — | — |
| handleCurrentChange | 列表分页事件（静态数据有效） | Function(currentPage) | — | — |
| searchCallback | 筛选回调事件（静态数据有效） | Function(searchData) | — | — |


## 使用

```vue
// main.js
import Vue from 'vue'
import ElPageTable from 'el-page-table'
Vue.use(ElPageTable)

// app.vue
<el-page-table class="el-page-table" :columns="columns"  />
```

1. columns 类型详解

```ts

interface ItemProps {
    label: string   // 表头显示名称
    prop:  string  // 表头数据字段
    render?: (scope) => VNnode  // 列表数据自定义展示
    showSearch?: boolean | object  // 查询项配置
    searchRender?: (searchData, prop) => VNnode  // 自定义查询项
    [key: string]: any  // 更多查看 el
}
const columns: Array<ItemProps> = [
    {
        label: '展示项名称',
        prop: '数据项字段'
    }
]
```

2. 静态列表

传入`columns, dataSource, total, currentPage, loading, handleCurrentChange...`

3. 组件内请求列表

传入`columns, searchData, query...`