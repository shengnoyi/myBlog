---
title: vue单文件组件初使用
date: 2017-05-11 10:45:41
tags:
---
### 使用webpack-simple
这一篇介绍了如何配置webpack，构建Vue单文件组件，参考了vue.js的官方模板[webpack-simple](https://github.com/vuejs-templates/webpack-simple)。
```
$ npm install -g vue-cli
$ vue init webpack-simple my-project
$ cd my-project
$ npm install
$ npm run dev
```

### node-sass
如果选择了使用sass选项，就会需要安装node-sass模块，安装node-sass模块时可能会出现安装不成功的问题，可以参考之前的文档[install node-sass](https://shengnoyi.github.io/2017/03/31/install-node-sass/)

### autoprefixer
写前端样式的时候，希望webpac自动帮忙补充一些兼容性前缀，需要安装postcss 和 autoprefixer
```
$ npm install --save postcss autoprefixer -d
```
在项目根目录新建一个postcss配置文件postcss.config.js:
``` bash
module.exports = {
  plugins: [
    require('autoprefixer')()
  ]
}
```
### babel-polyfill
好了，现在可以解析.vue文件了，也可以在.vue中使用sass语法，添加前缀。
我在测试babel转换es6语法的时候发现，es6的API---Promise在IE9浏览器中用不了（chrome可以，因为chrome实现了Promise)，原来babel只转换语法，不会帮你转换API,这个时候就要用到[babel-polifill](https://babeljs.io/docs/usage/polyfill/)

```
$ npm install --save babel-polyfill -d
```

安装完毕之后，在webpack.config.js的配置修改如下:
``` bash
module.exports = {
  entry: ['babel-polyfill','./src/main.js'],
};
```
在需要用到Promise.js的地方，使用es6语法:
``` bash
import "babel-polyfill";
```

### 自动生成发布时html
做完以上工作之后，我把构建之后的文件放在一个服务器上，发现一个问题，由于index.html是固定的，所以如果不手动改时间戳的话，浏览器就不会用新构建的.js文件，而是使用了浏览器缓存，所以必须加上时间戳，但每次发布都手动改实在很麻烦，解决方法：

使用webpack插件[html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin):
`$ npm install --save html-webpack-plugin -d`

参考配置如下，使用根目录下的文件index.tmpl.html作为构建模板，会生成一个.html到你的发布目录之中
``` bash
plugin:[
	new HtmlWebpackPlugin({
      title:'洋葱派',
      hash:true,
      template:__dirname+'/index.tmpl.html'
    })
]
```