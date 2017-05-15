---
title: install node-sass
date: 2017-03-31 17:12:50
tags: Vue
---
最近在使用vue单文件组件功能，需要在Window 7环境安装node-sass，遇到gyp error，提示没有安装python

解决方法：  
`npm install -g node-gyp`  

接着，这个工具是要安装python2.7还有一些c++包，下载可能很慢，要看运气，不过看源码可能直接下载后替代源文件，反正我是试了很多次在家里的时候直接安装成功了。  
`npm install --global --production windows-build-tools`

删掉之前装的node-sass
`npm rm node-sass -d`

设置sass淘宝镜像  
`set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/`  

安装  
`npm install node-sass -d`  

完成了！！！！

参考资料：
[安装 node-gyp](https://github.com/nodejs/node-gyp)  
[安装 node-sass 的正确姿势 ](https://github.com/lmk123/blog/issues/28)  
[...%1 is not a valid win32 application](https://github.com/sass/node-sass/issues/468)