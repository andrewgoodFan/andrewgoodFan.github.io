---
title: 微信小程序(二)
date: 2018-06-11 10:57:01
tags: 小程序 微信
---

## 小程序的一些问题( 二)

之前已经讲过了遇到的一些问题,现在是更新部分问题,主要是微信小程序B接口生成带参二维码和小程序模板消息的无限制发送.

### B接口生成带参二维码

首先查看[小程序官方文档](https://developers.weixin.qq.com/miniprogram/dev/api/qrcode.html),有三种模式的接口,我主要讲的是B接口,其他接口,请自行查看文档.

我的项目是后台获取二维码,前端将page参数传给后台,前端主要是传两个参数,
需要注意的是由于B接口生成的二维码暂时没有个数限制,很多项目都用这个,但是对于想进入对应的带参详情页面,scene是不可缺少的
现在官方文档对于scene的用法还是比较模糊的,我自己百度加线上测试了几次之后成功了.代码如下

**传给后台的参数**
```
page: 'pages/index/index',
scene: 'id=12&share=true'
```
**后台获取参数后需要做一下处理,不然获取的scene会不全** 
```
scene - scene.replace('=','/').replace('&','*')
```

扫码进入页面之后,前端需要获取码中scene的值,再做下一步处理
```
Page({
	  onLoad: function(options) {
    // options 中的 scene 需要使用 decodeURIComponent 才能获取到生成二维码时传入的 scene
    var scene = decodeURIComponent(options.scene)
    // 这时 scene= 'id/12*share/true'
  },
  getScene: function (options) {
		var scene = decodeURIComponent(options.scene)
		var obj ={}
		var arr =[]
		for (let i=0;i<scene.split('*').length;i++) {
			var arr = scene.split('*')[i].split('/')
			obj[arr[0]] = arr[1]
		}
		return obj
		obj = {
			id: 12,
			share: true
		}
  }
	})
```

之后就可以处理自己的的代码逻辑了

### 模板消息无限制发送

小程序中一般只能发送一次消息给其他人,有的项目在页面上埋点,获取几个formid保存起来,我自己是通过模板消息进入页面的时候,显示一个弹窗,
用户点弹窗之后就会更新个人的formid,然后可以再次发送消息...
模板消息需要特定的form才能配置
```
<!-- wxml -->

<form report-submit="true" bindsubmit="submit">
	<button form-type="submit"></button>
</form>
<!-- js -->
submit: function (e) {
	wx.request({
		url: 'XXXX',
		data: {
			form_id: e.detail.formId
		},
		method: 'POST',
		success (res) {

			},
			fail (err) {

			}
		})
}
// 需要注意的是 Android手机的form_id是一串数字,表示当前的时间戳,大概率上是不会重复的,
	 iOS手机的form_id是字符串,两种不同的显示,处理时最好综合两种...
```

火速更新......