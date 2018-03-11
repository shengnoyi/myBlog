---
title: canvas-article
date: 2017-10-17 11:51:33
tags: 小程序
---


根据微信头像和自定义的祝福语，选择好一个祝福语模板，点击确定即可进行祝福语图片的生成

## 主要思路
* 通过知晓云，存储祝福语模板的数据和图片文件，并获取微信用户的相关信息，用于后续绘制工作。
* 使用微信接口 `wx.getImageInfo` 获取网络图片的临时文件路径。
* 使用图片的临时文件和相关的数据信息，使用小程序的 canvas 接口进行图像绘制，绘制成功之后，使用 `wx.canvasToTempFilePath` 保存 canvas 的图像至临时文件，之后可使用 `wx.saveImageToPhotosAlbum` 保存图片到手机相册中

## 实现过程
### 资源加载
##### 获取微信头像和昵称 
  先使用 `wx.BaaS.login` 进行登录，若用户允许小程序获取用户个人信息，那么登录成功之后 `BaaS JS SDK` 会自动将用户的个人信息存储到小程序中，使用同步方法` wx.BaaS.storage.get('userinfo')` 即可获取，详见知晓云的[官方文档](https://doc.minapp.com/user/get-userinfo.html)
``` bash javascript
  onLoad() {
    let userInfo = wx.BaaS.storage.get('userinfo')
    // 设置用户个人信息
    const setUserInfo = (userInfo) => {
      let { nickName, avatarUrl } = userInfo
      this.setData({
        nickName,
        avatarUrl
      })
    }

    wx.showLoading({
      title: '请稍等',
      mask: true
    })

    // 尝试获取用户信息，若获取失败，则使用 wx.BaaS.login 进行登录
    if (userInfo) {
      setUserInfo(userInfo)

      // 加载祝福语模板的数据
      this.loadWishTemplates()
    } else {
      wx.BaaS.login().then(res => {
        let userInfo = wx.BaaS.storage.get('userinfo')
        setUserInfo(userInfo)

        // 加载祝福语模板的数据
        this.loadWishTemplates()
      }).catch(err => console.log(err))
    }
  },
```

##### 获取祝福语模板信息（主要是背景图的链接）
  为了能方便地添加新的模板，我们将模板的相关数据和图片文件都存储在知晓云中
  ![在知晓云中建立一个存放祝福语模板信息的表](https://cloud-minapp-3765.cloud.ifanrusercontent.com/1e4LP9rHqevWcskS.png!/fw/800)

  ![上传图片到知晓云](https://cloud-minapp-3765.cloud.ifanrusercontent.com/1e4LTqiNBMBsBGxD.png!/fw/1000)
### 展示效果图
由于 canvas 绘制图片需要耗费一定的时间，所以我们需要另外做一个的预览效果图，而不是直接使用 canvas 显示

![预览效果图](https://cloud-minapp-3765.cloud.ifanrusercontent.com/1e4Lh9vxFiACUvcs.png!/fw/350)

### 获取图片的临时文件用于 canvas 绘制
由于小程序的 canvas 接口 `canvasContext.drawImage` 并不能直接使用网络图片资源，所以我们应该将网络图片资源下载后再使用它们来进行图片绘制，相关的接口就是前面提到的 `wx.getImageInfo`, 当然，使用 `wx.downloadFile` 也能达到同样的效果
### 绘制过程
##### canvas 的显示控制
在第 2 步的时候没有直接使用 canvas 组件去做效果预览，为了页面的美观，将暂时用不到的 canvas 组件隐藏起来了。
由于小程序的 canvas 比较特别，当它在页面上不可见时，无法正常进行使用，也就是说，我们不能像平时在浏览器开发时设置 canvas 的 css 属性为 `display:none` 或者 `visibility:hidden`。
所以，在我们按下确定按钮，开始进行生成祝福语时, 做了如下处理：
调用微信接口 `wx.showloading(object)` 提示用户等待，并且设置 `wx.showloading(object)` 的参数 `object.mask = true`,使得用户在图片生成动作完成之前无法操作页面。
这时，小程序就偷偷地把 canvas 组件显示在用户看不到的页面下面，进行绘制过程。
在绘制过程完成之后，我们又会把 canvas 组件藏起来。

``` bash javascript
handleConfirm() {
  wx.showLoading({
    title: '正在生成图片',
    mask: true,
  })

  this.showCanvas()

  this.generateWishesImage().then((res) => {
    wx.hideLoading()
  }).catch(err => {
    console.log(err)
    wx.hideLoading()
  })
},
```

##### 网络资源加载
由于小程序的 canvas 无法直接使用网络图片进行绘制，所以当我们需要使用一个图片资源时，应该先使用 `wx.getImageInfo` 去获取该资源的临时文件。
这里分享一个小技巧吧，笔者上传的图片没有压缩，所以调用 `wx.getImageInfo` 加载图片还是比较久的，怎么提高加载速度又不用每次上传都手动去压缩图片呢？答案就是使用知晓云的图片云处理功能，使用给图片资源加上特定的后缀，可以利用图片云处理功能获得合适大小的图片，附上更详细的使用方法--[图片云处理](http://support.minapp.com/hc/kb/article/1082737/)
``` bash javascript
loadResources() {
  const { bgWishes, avatarUrl } = this.data
  const getImageInfo = (src) => {
    return new Promise((fulfill, reject) => {
      wx.getImageInfo({
        src,
        success({ width, height, path }) {
          fulfill(path)
        },
        fail() {
          reject(`获取图片失败:${src}`)
        }
      })
    })
  }

  // 存在知晓云服务器上的图片，可使用知晓云的图片云处理功能，提高加载速度
  const getZippedImage = ({ width = 300, src }) => {
    return `${src}!/fw/${width}`
  }

  // 一次性加载所有图片资源获取
  let promises = [getImageInfo(getZippedImage({ src: bgWishes })), getImageInfo(avatarUrl)]

  return Promise.all(promises).then(values => {
    return {
      bgWishes: values[0],
      avatarUrl: values[1],
    }
  })
},
```
##### 绘制
完成上一步的资源加载之后，我们终于可以开始画画了，使用 canvas 进行图片绘制，使用过 HTML5 的 canvas API 的同学应该都很熟悉吧。
值得一说的是 canvas 中的距离单位为 px, 附上坐标系示意图：
![canvas 的坐标系](https://mdn.mozillademos.org/files/224/Canvas_default_grid.png)
绘制的过程，调用 `wx.createCanvasContent(canvasID)` 获取画布的上下文 ctx，依次完成绘制背景、祝福语的内容、微信用户头像和昵称、祝福人数等信息的逻辑，本文的 demo 主要是做了绘制图片和绘制文字两个工作。

###### 绘制图片
调用 `ctx.drawImage(resources, x, y, width, height)`即可，`resource` 需要使用我们之前获得的临时文件路径。

###### 绘制文字
这个比绘制图片稍微复杂一些，比如绘制祝福语的内容时，用户可能输入很长的字符串，为了美观，我们需要按照一定规则将它们分组换行。
在本文的示例代码中，为了方便演示，我使用了比较简单的规则，每十六个字符为一行，有兴趣的读者可以考虑自己丰富这一块的逻辑，考虑中英文、符号等特殊情况。
确定好要绘制的单行文本之后，使用 `wx.setFontSize` 和 `wx.setTextAlign` 分别设置字体大小和字体对齐方式，关于接口 `ctx.setTextAlign` 的使用，请参考示意图：
![canvasContext.setTextAlign 示意](https://mp.weixin.qq.com/debug/wxadoc/dev/image/canvas/set-text-align.png?t=20171013)
附上 demo 绘制逻辑的代码：
``` bash javascript
drawImage({ resources }) {
  const ctx = wx.createCanvasContext('myCanvas')
  const { bgWishes, avatarUrl } = resources
  const { nickName, count, wishesContent } = this.data
  const CANVAS_W = 300
  const CANVAS_H = 375

  // draw background
  ctx.drawImage(bgWishes, 0, 0, CANVAS_W, CANVAS_H)

  // draw wishes content
  const getWishBlocks = (content = '') => {
    let result = []
    if (typeof content === 'string') {
      // 将文字简单分行，每十六个字符为一行
      const COUNT_PER_BLOCK = 16
      for (let offset = 0, l = content.length; offset < l;) {
        let start = offset
        let end = offset + COUNT_PER_BLOCK
        let block = content.substring(start, end)
        result.push(block)
        offset += COUNT_PER_BLOCK
      }
    }
    return result
  }
  const WISHES_X = CANVAS_W / 2
  const WISHES_Y = 90
  const WISHES_LIGHT_HEIGHT = 21
  ctx.setTextAlign('center')
  ctx.setFontSize(15)
  let wishesContentBlocks = getWishBlocks(wishesContent)

  for (let i = 0, l = wishesContentBlocks.length; i < l; i++) {
    let x = WISHES_X
    let y = WISHES_Y + WISHES_LIGHT_HEIGHT * i
    ctx.fillText(wishesContentBlocks[i], x, y)
  }

  // draw userInfo
  //draw avatar
  const AVATAR_X = CANVAS_W - 5 - 53
  const AVATAR_Y = CANVAS_H - 104 - 15
  const AVATAR_SIZE = 53
  ctx.drawImage(avatarUrl, AVATAR_X, AVATAR_Y, 53, 53)
  // draw avatar border

  // draw nickname
  const NICKNAME_X = CANVAS_W - 5
  const NICKNAME_Y = CANVAS_H - 20 - 5 - 15
  ctx.setTextAlign('right')
  ctx.setFontSize(15)
  ctx.fillText(nickName, NICKNAME_X, NICKNAME_Y)

  // draw wishes count
  const COUNT_X = NICKNAME_X
  const COUNT_Y = CANVAS_H - 15
  ctx.setTextAlign('right')
  ctx.setFontSize(13)
  ctx.fillText(`第${count}位送出祝福`, COUNT_X, COUNT_Y)

  ctx.draw()

  return ctx
}
```
### 保存
在完成绘制过程之后，我们需要将 canvas 中辛辛苦苦绘制的图像保存下来。
调用 [`wx.canvasToTempFilePath(OBJECT)`](https://mp.weixin.qq.com/debug/wxadoc/dev/api/canvas/temp-file.html)，可以获得图像的临时文件路径，这时，我们使用该路径，就可以把它显示在 `<image/>` 标签中了。
如果需要把图片保存下来，可以调用 `wx.saveImageToPhotosAlbum(OBJECT)`, 把临时文件保存到手机相册中。

##
好了，关于如何使用 canvas 绘制祝福语图片的教程就到此结束了。