---
title: vue-preload
date: 2021-06-15 14:07:46
tags:
 - 前端
 - Vue
categories:
 - 前端
 - Vue
---

## vue预渲染方案

### 下载插件

`npm install prerender-spa-plugin vue-meta-info`

### 配置插件

```js
// webpack.prod.config.js

const PrerenderSPAPlugin = require('prerender-spa-plugin')
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer

const NODE_ENV = process.env.NODE_ENV


if (NODE_ENV === 'production') {
  webpackConfig.plugins.push(
    // prerender
    new PrerenderSPAPlugin({
      // Required - The path to the webpack-outputted app to prerender.
      staticDir: config.build.assetsRoot,
      // Required - Routes to render.
      routes: settings.prerenderRoutes,
       // 这个很重要，如果没有配置这段，也不会进行预编译
       renderer: new Renderer({
        inject: {
            foo: 'bar'
        },
        headless: false,
        // 在 main.js 中 document.dispatchEvent(new Event('render-event'))，两者的事件名称要对应上。
        // renderAfterDocumentEvent: 'render-event'
      })
    })
  )
}

// main.js

mounted() {
  document.dispatchEvent(new Event('custom-render-trigger'))
}
```