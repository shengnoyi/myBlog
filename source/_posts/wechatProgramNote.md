---
title: wechatProgramNote
date: 2017-03-17 15:35:50
tags: 小程序
---
## 微信小程序开发笔记
参考资料：[小程序api](https://mp.weixin.qq.com/debug/wxadoc/dev/api/api-login.html)
### 登录态维护

登录逻辑：  

- 首次登录：发送code->等待第三方服务器response一个`3rd_session`->写入storage，使用`3rd_session`与第三方服务器通信
- 非首次登录：使用微信提供的`wx.checkSession()`，检测登录态`session_key`是否仍然有效->如果仍然有效，使用`3rd_session`(依赖于`session_key`)与第三方服务器通信;无效，则需重新执行首次登录的逻辑，获取有效的`3rd_session`.

时序图-1
![时序图](https://mp.weixin.qq.com/debug/wxadoc/dev/image/login.png?t=2017213)

### Question
Q:`wx.checkSession()`检测的登录态的作用范围是什么？是仅限于第三方服务器和微信服务器吗？如果登录态过期了，还能通过`wx.request()`向第三方服务器发出请求吗？



### 组件使用篇
`<navigator>`使用**相对路径**，需要根据情况设置`open-type`才能正确跳转。

### app.json配置
页面需要配置pages数组才能正常使用，如果需要新建一个新的页面，可以先在app.json里面配置，这样就会自动生成文件目录

### 语法
##### 1.data-属性
``` bash
	<view class="label-list" bindtap="pickLabel">
      <view class="label-item" wx:for="{{labels}}" wx:key="{{item.id}}" data-label="{{item.id}}" 
		   hover="true" hover-class="picked">
        <text>{{item.name}}</text>
      </view>
    </view>
```

页面要求对某个数据列表进行事件监听，我在`label-list`上绑定了一个tap事件handler,但是使用`e.target.dataset.label`获取数据`label`的时候，却时好时坏，有些情况能获取正确的label，有时是undefined。

查了资料后发现,原来event对象中的target、currentTarget分别代表被点击的元素和当前捕获/冒泡事件的函数，由于我的`<text>`没有`data-label`属性，点击文本时所以获取的自然是`undefined`啦，如果点击item的空白处，则能正确得到data-属性  
** 说到底还是因为小程序的事件绑定的函数并不能传参导致的，如果能传参，根本不需要多设置一个data-属性**

##### 2.使用setData()
只有使用`setData`才能把数据正确发送给视图层，而不是直接修改`this.data.property`,由于开发者工具支持es6语法，有时候只需要重设某些属性的时候，可以先使用`Object.assign`复制一个数据，修改之后，再使用`setData`
##### 3.使用template
注意这个`template`真的只是**template！！！**无法附带js逻辑或者wxss样式。   
你可以在用到该`template`的地方使用`@import`导入样式文件。  
逻辑的话可以在`template`的元素上绑定事件响应函数，而该函数应该在父页面里的js文件里实现。

### 常见Error
- 在页面内使用了`template`,然后出现了`firstRender not the data from Page.data`，设置`template`的`data`属性的时候，必须在初始化`this.data`，可以传`{}`，不能不定义，即使你在onload那里使用了`setData`

### 其他问题
- 小程序不支持Promise,可以在网上找支持Promise的polyfill（我用的是es6-promise）