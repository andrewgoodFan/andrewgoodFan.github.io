---
title: 微信小程序
date: 2018-05-04 14:55:34
tags:
- 前端
- 微信小程序
categories:
- 前端
- 微信小程序
---
## 谈谈我所遇到的小程序的问题

主要是记录一下在做的项目中遇到的一些问题和粗浅的解决办法,如果各位大神有更好的方法,希望能不吝赐教。

### 小程序登录拦截

现在官方的小程序路由拦截还没出现,都是要靠自己写的,我是在首页onLoad中判断储存的登录态是否存在，然后checksession，比较麻烦，最近知道一个可以运行在小程序的请求库，fly.js，有兴趣的可以自行学习使用。

### Ios时间转换问题

```
var date = '2018-05-30 16:00:00';
var tempdate = new Date(date);
```

上述代码在模拟器和Android手机上运行没问题,但在ios上就会是NAN,原因是ios不识别'-'形式的时间格式
将其转化成

```
var date = '2018-05-30 16:00:00';
data = date.replace(/-/g, '/');
var tempdate = new Date(date);
```

这样就可以计算时间的转换了.

### 分享内页时无法跳转到首页

1. 在内页上添加一个可移动的悬浮块,跳转到想要的页面上(低配);
2. 分享首页带上具体页面你的参数,在首页onload判断参数,跳转到具体的页面.

### 图片自适应

有些时候,将图片全部设为同样的大小,并不完美,比较小的图片会被放大,模糊,可以设置一个最大宽度限制,小于次宽度的图片
就以原图的大小显示

```
<!-- wxml -->
 <image wx:for="{{photos}}" wx:key="{{index}}" wx:if="{{item}}" src='{{imgHost+item}}' bindload='imgLoad' data-imgid="{{index}}" style="width:{{imgwidth[index]}}rpx; height:{{imgheight[index]}}rpx;"></image>

 <!-- js -->
 Page({
 		data: {
 			photos: [],
 			imgwidth: [],
 			imgheight: []
 		},
 		imgLoad: function (e) {
      var imgid = e.currentTarget.dataset.imgid
      var _this = this;
      var $width = e.detail.width,    //获取图片真实宽度  
          $height = e.detail.height,
          ratio = $width / $height;   //图片的真实宽高比例  
            if($width>650) {
                var viewWidth = 650,    
                viewHeight = 650 / ratio; 
            }else {
                var viewWidth = $width,
                    viewHeight = $height;
            }
            var itemwidth = 'imgwidth[' + imgid + ']'  //设置图片显示宽度，
            var itemhieght = 'imgheight['+imgid+']'    //计算的高度值  
              
               
      this.setData({
          [itemwidth]: viewWidth,
          [itemhieght]: viewHeight
      })  
      console.log(this.data.imgwidth,this.data.imgheight)
  },
 	})
```

### 分享到朋友圈

小程序暂时没有开放直接分享到朋友圈的API,需要用户保存截图再分享
```
 const shareApp = () => {
 			//1. 获取当前页面二维码
 			
     //2. canvas绘制文字和图片
     const ctx = wx.createCanvasContext('myCanvas');
     var imgPath = '/image/icon/doyen.png'
     var bgImgPath = '../../../image/doyen.png';
     ctx.drawImage(imgPath, 0, 0, 600, 520);

     ctx.setFillStyle('white')
     ctx.fillRect(0, 520, 600, 280);

     ctx.drawImage(imgPath, 30, 550, 60, 60);
     ctx.drawImage(bgImgPath, 30, 550, 60, 60);
     ctx.drawImage(imgPath, 410, 610, 160, 160);

     ctx.setFontSize(28)
     ctx.setFillStyle('#6F6F6F')
     ctx.fillText('text1', 110, 590)

     ctx.setFontSize(30)
     ctx.setFillStyle('#111111')
     ctx.fillText('text2', 30, 660)
     ctx.fillText('text3', 30, 700)

     ctx.setFontSize(24)
     ctx.fillText('text4', 30, 770)
     ctx.draw()
     // 3. canvas画布转成图片
     wx.canvasToTempFilePath({
         x: 0,
         y: 0,
         width: 600,
         height: 800,
         destWidth: 600,
         destHeight: 800,
         canvasId: 'myCanvas',
         success: function (res) {
             console.log(res.tempFilePath);
             that.setData({
                 shareImgSrc: res.tempFilePath
             })

         },
         fail: function (res) {
             console.log(res)
         }
     })

     //4. 当用户点击分享到朋友圈时，将图片保存到相册
     wx.saveImageToPhotosAlbum({
         filePath: that.data.shareImgSrc,
         success(res) {
             wx.showModal({
                 title: '存图成功',
                 content: '图片成功保存',
                 showCancel: false,
                 confirmText: '确定',
                 confirmColor: '#72B9C3',
                 success: function (res) {
                     if (res.confirm) {
                         console.log('用户点击确定');
                     }
                     //  that.hideShareImg()
                 }
             })
         },
         fail: res => {

             console.log(res)
         }
     })

 }
```

canvas必须显示在页面上才能保存图片成功,但是我们又不想让用户看见这张图,这时候可以用定位,有多远就将canvas定位到多远.

### 页面栈
小程序最多存在五个页面栈,跳转方式可以根据项目需要来定义.

### 关于一些组件的使用

#### 自定义组件
一些基本的用法可以参照小程序官方文档,

##### 调用子组件方法

```this.selectComponent("#组件id").method();```

#### textarea的层级问题

textarea 组件是由客户端创建的原生组件，它的层级是最高的，不能通过 z-index 控制层级。

持续更新中......