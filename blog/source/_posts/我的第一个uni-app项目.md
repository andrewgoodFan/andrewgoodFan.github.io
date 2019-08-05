---
title: 我的第一个uni-app项目 (一)
date: 2019-06-20 10:31:54
tags:
 - 前端
 - uni-app
categories:
 - 前端
 - uni-app
---

## 项目背景

公司需要开发一个微信小程序,由我来负责前端,最近我一直在学习uni-app这个框架,说是多端通用,我看了文档,一些标签和api,都是接近小程序的,app端也可以通用,运用的是vue的写法,之前我是用过vue的,上手很快,而且uni-app更新和修复bug也是很及时的,我加上了一个uni-app入坑讨论群,宣布正式入坑uni-app.

## 一些话

第一次使用uni-app,心里是激动又忐忑,学的东西终于有用武之地了,但是又怕是一个深坑,爬不上来,还好的是这个项目不算大, 还是选择相信ui-app,相信Dcloud, 话不多说, 还是讲讲遇到的一些坑吧...

## 项目搭建方式
我使用的是 HbuilderX 2.0.1.20190614,构建项目.
## 页面和样式

### 单位
uni-app使用的是全新的像素单位upx, 也是以750px为基准的, 也就是说如果你拿到的设计稿宽度是750的,那个你就可以直接 `1px = 1upx` 这么使用,
如果不是750的,那么需要小小的计算一下 `750 * 元素在设计稿中的宽度 / 设计稿基准宽度`

### css

推荐使用flex布局,兼容nvue和快应用,详情可以网上了解.
在`app.vue`中引入的样式,即为全局样式,使用hbuilderx构建项目的时候,自带一个`uni.scss`文件,里面可以设置一些全局样式变量,前提是你使用scss写样式,所以,我毫不犹豫的选择了scss来写样式.

动态style我采用 `upx2px` 方法绑定值

动态class绑定, 我使用对象形式 `:class={'className':  Variable, 'className': Variable}`

### css变量

推荐在使用定位时,一些特定位置使用css变量.

1. var(--status-bar-height) 此变量在微信小程序环境为固定 25px，在 5+App 里为手机实际状态栏高度。
2. 当设置 "navigationStyle":"custom" 取消原生导航栏后，由于窗体为沉浸式，占据了状态栏位置。此时可以使用一个高度为 var(--status-bar-height) 的 view 放在页面顶部，避免页面内容出现在状态栏。
3. 由于在H5端，不存在原生导航栏和tabbar，也是前端div模拟。如果设置了一个固定位置的居底view，在小程序和App端是在tabbar上方，但在H5端会与tabbar重叠。此时可使用--window-bottom，不管在哪个端，都是固定在tabbar上方。

### 传值

我使用vuex传值,但是有些时候,只需要在两个页面之间传递,使用vuex的话,需要定义变量,比较不划算, 期望后期能出现类似eventBus的监听, 使用storage也可以做到两个页面之间传值

### 全局变量

我使用`Vue.mixin()` 定义全局方法或者使用`Vue.prototype` 挂在到原型上,

```
Vue.mixin({
    onShareAppMessage () {
        console.log('mix-share')
    },
    methods: {
        navigateTo (url) {
            uni.navigateTo({
                url
            })
        },
        redirectTo (url) {
            uni.redirectTo({
                url
            })
        },
        navigateBack (delata = 1) {
            uni.navigateBack({
                delta: delata
            })
        },
        relanchTo (url) {
            uni.reLaunch({
                url
            })
        },
        swithTab (url) {
            uni.switchTab({
                url
            })
        },
        toast (config = {}) {
            if(!config.title)return;
            uni.showToast({
                title: config.title,
                icon: config.icon || 'none',
                mask: config.mask || false,
                duration: config.duration || 2000
            })
        }
    }
})
```

uni-app支持像小程序一样的globalData, 在`app.vue`中
```
 export default {  
     globalData: {  
         meg: 'hello world'  
     },  
     onLaunch: function() {  
         console.log('App Launch')  
     },  
     onShow: function() {  
         console.log('App Show')  
     },  
     onHide: function() {  
         console.log('App Hide')  
     }  
 }  
```
### 条件编译

推荐在使用平台特定api时使用条件编译,这方面我觉得还是很有必要主要的...

### 运行环境

我是通过node环境变量来控制host, 在`main.js`中引入
```
let base = {
    baseUrl: process.env.NODE_ENV === 'development' ? 'http://development.com/api' : 'http://production.com/api'
}
export default base;
```
### 使用easy-mock

前端开发,有时候需要等后端的接口,在去联调,我在这里使用了一个模拟数据的工具,可视化配置,上手简单,我根据接口文档编写接口,写自己的模拟数据,然后再自己调用接口,之后,只需要修改请求host即可,大大减少了前后端联调时间.

