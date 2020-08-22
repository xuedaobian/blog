---
title: webpack基础学习
date: 2020-07-24 13:12:15
tags:
- webpack
- 前端工程化
---

# 一、webpack的历史

<!--more-->

浏览器加载脚本的两种思想：1.多脚本，2.大脚本。多脚本容易引起网络瓶颈，大脚本会有作用域，全局污染问题，但是通过**立即执行函数**解决了。而此时的 `grunt` 等工具此时即解决了脚本数量问题（通过跨脚本引用，拼接），又有**`IIFE`**解决作用域问题了，看起来很美好。

但是跨脚本引用时导致了一个问题，如果一个脚本我只需要他里面的一个方法，但是通过 `grunt` 这种类型的工具却要引入一整个脚本或者说与这个脚本相关的整个库，导致最终的构建结果冗余了过多不需要的代码，而人为去修改则是过于繁杂。

此时`nodejs`发布了 CommonJS ，并引入了 `require` 机制，它允许你在当前文件中加载和使用某个模块。导入需要的每个模块，这一开箱即用的功能，帮助我们解决了作用域问题。导致在 **`IIFE`** 的光彩失色许多。开发者都想用模块化的思想来编写 JavaScript 代码，但是浏览器还未支持这些新思想。因而创建了 Browserify, RequireJS 和 SystemJS 等打包工具，允许我们编写能够在浏览器中运行的 CommonJS 模块。

正是因为现在流行模块化开发但是浏览器又不支持，于是webpack这类工具----可以编写模块（本身支持 js 语法），又支持不同种类的模块（通过`loader`来支持），同时将这些模块转化为浏览器可以识别运行的文件类型。在这过程中的不同时期通过一些插件使得输出的文件变得简洁，加快构建速度等。

# 二、基础知识

现在流行通过 webpack.config.js 来编写 webpack 配置。

首先要明白的一点是，**webpack 是运行在 node 上的**

必含：

- entry 入口，webpack 打包整个项目的入口文件，一般接受一个对象，key 对应着 output 的[name] ，value则是入口文件路径，可以是字符串/数组，总之具体到某一元素时则必须是合理的noderequire函数参数字符串。
- output 出口，webpack 运行结束之后生成的文件存放的位置和命名。path 属性表示存放地点，publicPath 表示无论是开发模式还是生产模式都可以存放的地点，filename 表示主文件名字。
- module 直译器，在这个字段里利用不同的 loader 来处理不同的模块。
- plugins 插件，当我们想要使用除了 loader 能提供的功能，我们就可以在这其中引入相应功能的组件。

# 三、loader

webpack 本身只能解析、处理js 类型的文件依赖，但是通过使用 loader 我们可以利用模块在 webpack 的不同含义来处理非 js 的文件。

webpack 能识别的模块依赖关系

> - [ES2015 `import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 语句
> - [CommonJS](http://www.commonjs.org/specs/modules/1.0/) `require()` 语句
> - [AMD](https://github.com/amdjs/amdjs-api/blob/master/AMD.md) `define` 和 `require` 语句
> - css/sass/less 文件中的 [`@import` 语句](https://developer.mozilla.org/en-US/docs/Web/CSS/@import)。
> - 样式(`url(...)`)或 HTML 文件(``)中的图片链接

支持的模块类型请 [参考](https://v4.webpack.docschina.org/loaders) 

注：**loader 是链式反向调用** ，因为 webpack 打包是通过从入口文件查看依赖，然后进入依赖文件，进入文件后查看依赖，进入-查看。。。无线套娃，直到最后没有依赖。这样的递归进入文件打包，所以最后执行完毕的反而是最开始的 loader。其中每个 loader 承接着上一个已经处理过的资源（有没有动态规划那味？），最后一个则返回 webpack 期望返回的 JavaScript 代码。

# 四、plugin 插件

尤雨溪大佬在一篇文章说到：“webpack 中有一种 ‘ **一切皆插件** ’ 的思想。”而 webpack 官网中也是这么形容

> Plugins are the backbone of webpack. webpack itself is built on the same plugin system that you use in your webpack configuration!
>
> 翻译过来就是：插件是 webpack 的 支柱 功能。webpack 自身也是构建于，你在 webpack 配置中用到的相同的插件系统之上！

插件就是解决 loader 不能做到的任何事。这就是插件。

webpack **插件**是一个具有  `apply` 方法的 JavaScript 对象。`apply` 方法会被 webpack compiler 调用，并且 compiler 对象可在**整个**编译生命周期访问。

每个插件有不同的作用，[参见](https://www.webpackjs.com/plugins/):eye_speech_bubble: