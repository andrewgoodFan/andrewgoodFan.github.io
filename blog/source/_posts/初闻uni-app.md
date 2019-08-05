---
title: '初闻uni-app'
date: 2019-05-17 17:14:12
tags:
 - 前端
 - uni-app
categories:
 - 前端
 - uni-app
---

## 什么是uni-app

&nbsp;&nbsp;&nbsp;&nbsp;uni-app 是一个使用 Vue.js 开发跨平台应用的前端框架，开发者编写一套代码，可编译到iOS、Android、H5、小程序等多个平台，对于熟悉 Vue.js 和微信小序的人来说，uni-app 可以说是零成本。

官网地址：
[https://uniapp.dcloud.io](https://uniapp.dcloud.io)

## uni-app基础

&nbsp;&nbsp;&nbsp;&nbsp;首先，我把介绍的视频看了一遍，然后学习官方文档，本文是我学习[<font color=#fc6423>uni-app官方文档</font>](https://uniapp.dcloud.io/collocation/pages)整理的一些笔记。

### 项目目录

&nbsp;&nbsp;&nbsp;&nbsp;我是用的hubuilderX编辑器学习的，直接在编辑器上新建项目，选择uni-app，可以快速构建uni-app项目，第一次推荐使用hello uni-app模板，点击运行到浏览器，即可在浏览器中预览，包括基础组件，api，扩展组件，一些模板，可以直接拿来用，uni-app推荐使用基础组件，基础组件满足不了的再使用扩展组件。

### 配置

`pages.json` 文件用来对 uni-app 进行全局配置，决定页面文件的路径、窗口样式、原生的导航栏、底部的原生tabbar 等。它类似微信小程序中app.json的页面管理部分。

#### 配置项列表

uni-app的配置项，感觉和微信小程序的配置很像，分类全局配置和单独页面的配置，对于不同的平台扩展了其独有的导航和一些独特设置，可以使用条件编译达到部分想要的效果。

#### globalStyle

用于设置应用的状态栏、导航条、标题、窗口背景色等。

#### pages

用于设置单个页面的路径、导航条、标题、窗口背景色等。
pages数组的第一项是起始页，新增页面是时也需要在pages里面添加页面信息。

#### condition

添加编译模式，配置初始页，和小程序开发者工具的编译模式一样一样的。

#### tabbar

底部导航栏的配置，一般是数组。2-5项，配置导航icon、文字、选中样式，现在添加了可以自定义，之前都没有，在H5端是div模拟导航栏，这样会出现一个问题，这个导航是在页面范围之内的，所以bottom为0的按钮会被覆盖，为此uni-app提供了几个css变量，可以解决这个问题。
`--status-bar-height` 系统状态栏高度
`--window-top` 内容区域距离顶部的距离
`--window-bottom` 内容区域距离底部的距离

现在导航栏可以自定义，也可以操作更改文字、icon、显示或隐藏，添加角标文字，越来越灵活了，之前的时候，原生导航达不到的效果，只能自己模拟tabbar，跳转体验很不好。
