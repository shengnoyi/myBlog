---
title: install node-sass
date: 2017-03-31 17:12:50
tags: Vue
---
Window环境安装node-sass遇到gyp error，提示没有安装python
参考[解决方案](https://github.com/nodejs/node-gyp)后，发现在windows下先`npm install -g node-gyp`再`npm install --global --production windows-build-tools`就解决了，
一开始我没看清楚，还安装了python 2.7和visual c++的一些开发环境，等到设置环境变量的时候才发现有更方便的方法。