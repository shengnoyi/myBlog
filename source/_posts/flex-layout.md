---
title: flex layout
date: 2017-03-31 09:51:59
tags: 前端开发
---
最近在用做移动端项目的时候，遇到一个需求，需要实现可以左右滚动的布局,里面有多个div。由于最近在用弹性布局，所以希望用弹性布局来做，而不是浮动方式，下面是实现方案。
style
``` bash
.flex-container{
  overflow-x: auto;
  display: flex;
  width:300px;
}
.flex-item{
  background-color: #af757f;
  width:100px;
  height: 100px;
  flex-shrink: 0;
  margin-right: 10px;
}
```
html
``` bash
<div class="flex-container">
  <div class="flex-item"></div>
  <div class="flex-item"></div>
  <div class="flex-item"></div>
  <div class="flex-item"></div>         
</div>
```
关键属性就是`flex-shrink`啦，它能决定弹性布局元素的收缩性，设置为0的时候不收缩。