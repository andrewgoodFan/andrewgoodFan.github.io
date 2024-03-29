---
title: 发布一个插件到npm
date: 2021-05-19 12:00:17
tags:
 - 前端
 - components
categories:
 - 前端
 - components
---


## 发布一个vue插件到npm

### 编写插件

#### 搭建项目

1. 使用`vue-cli3`搭建项目

```
vue create el-page-table
cd el-page-table
npm run serve
```
2. 创建组件目录`libs`
3. 使用vue自定义组件形式编写组件`libs -> PageTable.vue`
4. 在组件根目录 `libs`下创建 `index.js`导出组件

```jsx
// libs/index.js
import PageTable from './PageTable.vue'

const install = (Vue) => {
    Vue.component(PageTable.name, PageTable)
}

if (typeof window !== 'undefined' && window.Vue) {
    install(window.Vue)
}

export default {
    install,
    PageTable
}

vue插件编写方式，详情可查看官网
```

5. 测试使用插件

```jsx
// main.js
import ElPageTable from './libs/index'
Vue.use(ElPageTable)

// app.vue
<el-page-table class="el-page-table" :columns="columns"  />
```
6. 打包插件

```js
// vue.config.js  打包配置文件
module.exports = {
    css: {
        extract: false  // css 使用内联，打包后不会出现css文件，不需要额外引入样式文件，如果样式文件较大，不建议使用
    },
    productionSourceMap: false // 去掉sourceMap
}

// package.json

// vue-cli3 可以将一个单独的入口构建为一个库 详情可插件vue-cli3官网

"scripts": {
    "lib": "vue-cli-service build --target lib --name el-page-table --dest lib src/libs/index.js"
}
```
  - `lib/el-page-table.common.js`：一个给打包器用的 CommonJS 包 (不幸的是，webpack 目前还并没有支持 ES modules 输出格式的包)
   
  - `lib/el-page-table.umd.js`：一个直接给浏览器或 AMD loader 使用的 UMD 包
   
  - `lib/el-page-table.umd.min.js`：压缩后的 UMD 构建版本
   
  - `lib/el-page-table.css`：提取出来的 CSS 文件 (可以通过在 `vue.config.js` 中设置 `css: { extract: false }` 强制内联)

7. 配置npm

```js
// .npmignore
配置发布忽略文件 大体上可以只留打包之后的 lib, package.json, LICENSE 等

// package.json 

配置组件信息
{
    ...
    "name": "el-page-table", // 插件名称，注意不要有重名，可提前去npm库搜索
    "version": "1.0.0", // 每次发布需修改版本号
    "author": "Foley Fan",
    "description": "el-page-table components", // 插件描述
    "main": "lib/el-page-table.umd.min.js", // 插件入口文件
    "keyword": "el-page-table",  // 关键字搜索
    "license": "MIT", // 开源协议
    "private": false, // 是否私有，需要修改为 false 才能发布到 npm
    ...
}
```

8. 发布npm

  - 使用淘宝镜像的，需要切换到npm上，推荐一个npm源管理工具 ` nrm `
  - 没有npm账号的，可以添加账号 ` npm adduser `
  - 登录npm `npm login`  输入账号、密码、邮箱
  - 发布 `npm publish`