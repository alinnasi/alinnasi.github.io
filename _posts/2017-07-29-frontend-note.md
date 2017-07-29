---
title: 前端工具链学习笔记
date: 2017-07-29
category: 前端
---

# 前端工具链学习笔记

## NPM

NPM是NodeJS的包管理工具，后来也被用在前端项目的依赖管理上，使用NodeJS工具链的前端项目，最终都要通过打包工具打包成纯前端的JS代码。

初始化项目：

```
npm init
```

初始化后，项目目录会多出一个名为`package.json`的文件，内容如下：

```
{
  "name": "npm-test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

主要的依赖管理命令如下：

```
// 安装最新依赖
npm install jquery

// 安装指定版本
npm install jquery@1.11.0

// 安装指定版本的同时，保存到package.json
npm install --save jquery@1.11.0

// 安装指定版本的同时，作为开发依赖保存到package.json
// 相当于maven中的provided scope
npm install --save-dev jquery@1.11.0

// 安装在全局位置，用于安装命令行工具
npm install -g webpack

// 从package.json安装所有依赖
npm install

// 删除依赖
npm uninstall --save jquery
```

## Webpack

模块的依赖关系，以及模块变量的作用域，是模块化需要解决的两个问题。目前JavaScript有几种模块化规范：

* CMD (SeaJS)
* AMD (RequireJS)
* CommonJS (NodeJS)
* ES6 Module (ECMAScript 2015)

目前使用得比较多的是CommonJS，其语法也很简单，比如为了解决模块间的依赖问题，CommonJS规定了如下语法来声明对其他模块的依赖：

```
require('foo.js')
```

为了解决变量作用域问题，规定了如下语法来导出变量：

```
module.exports = {
  bar: 123
}
```

除此之外，还需要一个符合CommonJS规范的打包工具，比如Webpack。使用NPM安装Webpack，安装在全局位置：

```
npm install -g webpack
```

在我的机子上，安装后位置在：

```
/usr/local/bin/webpack -> ../lib/node_modules/webpack/bin/webpack.js
```

`webpack.js`是一个JS脚本，通过node运行，这就是为什么这一套工具链需要node运行环境的原因。

接下来实践一下，我们需要两个文件，首先是`foo.js`，它只导出了一个变量：

```
module.exports = { bar: 123 }
```

然后是`entry.js`，它依赖`foo.js`，并打印后者导出的变量：

```
var foo = require('./foo.js')
console.log(foo.bar)
```

接下来就可以进行打包了，命令如下：

```
webpack entry.js --output-filename build/output.js
```

没有意外的话，`build.output.js`就是打包的结果。

## 参考

* [模块化工具及组件化思想](https://segmentfault.com/a/1190000007661023)
    