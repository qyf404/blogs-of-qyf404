[](我的前端技术栈(2018版))

![](./logos/nodejs_logo.png)

# 为什么想写这篇文章

工作这些年我所做的事情总是循环着，刚工作的时候html、js、jQuery用的也很溜，可是后面的七年都没写过前端代码。现在由于项目原因，时不时的需要去写点前端代码，可是前端的世界已经在nodejs的帮助下发生了翻天覆地的变化，一个又一个的框架层出不穷。这篇文章记录下我这一路披荆斩棘遇到的前端技术。一来帮助自己梳理知识体系，二来帮助前端新手明确学习路径和学习目标。

# vue项目的技术栈

我目前的项目用的是vue2，这里罗列的也都是基于vue或者vue整合的技术。基本上按照顺序把一个一个技术搞懂了，用vue做个前端项目是没什么问题了。

## nodejs

[Node.js](https://nodejs.org/en/)就是运行在服务端的JavaScript。是一个基于Chrome V8 JavaScript引擎的JavaScript运行环境。在node.js出现之前，JavaScript是只能在浏览器中运行的，Node.js的出现可以让开发像写python一样在命令行写JavaScript。

## nvm

[nvm](https://github.com/creationix/nvm)是一个node.js的版本管理工具。可以帮助开发管理本地使用的多个node.js版本。

## npm、yarn

[npm](https://www.npmjs.com/)是一个js的包管理工具，它的出现真的是让前端技术飞速发展。它做的事情就想java中的maven。

[yarn](https://yarnpkg.com/zh-Hans/)和npm一样是个js的包管理工具，不过它在下载依赖时会做全局缓存，不会重复下包。

## vue

[vue](https://vuejs.org/)是一套构建用户界面的渐进式框架。它最大的优势就是组件化，用这个框架可以把很多元素封装成组件，最后功能的开发变成了组件的拼装，极大的提高了代码的复用。

## vue-cli

[vue-cli](https://cli.vuejs.org/)是一个脚手架，能帮助开发快速创建一个vue项目。

## bootstrap

[bootstrap](http://getbootstrap.com/)是一个前端技术的开源工具包。虽然bootstrap提供了js库，但是很多项目更多的主要是使用了它的css样式库。

## bootstrap-vue

[bootstrap-vue](https://bootstrap-vue.js.org/)是一个vue的组件库，把bootstrap的组件封装成了vue的组件。

## ramda

[ramda](https://ramdajs.com/)是一个函数库，提供了很多处理js对象的方法。

## axios

[axios](https://github.com/axios/axios)是一个前端异步执行http请求的ajax框架。

## karma

[karma](https://github.com/karma-runner/karma)是一个测试运行器，可以让测试跑在一些主流的浏览器中。karma不是测试框架，它只是启动了一个HTTP服务，所以你需要在karma启动时配置测试框架。karma支持多种测试框架：jasmine，mocha，QUnit

## mocha

[mocha](https://mochajs.org/)是一个单元测试框架，用它可以更容易的测试异步执行的方法。而且可以产出一个漂亮的测试报告。

## chai

[chai](http://www.chaijs.com/)是一个断言库，它封装了很多方法，能让开发写出更接近自然语言的代码（语义化编程）。一般它会和Mocha一起使用，就像[Junit](https://junit.org/)和[Hamcrest](http://hamcrest.org/)配合使用一样。

## validate.js

[validate.js](https://validatejs.org)是一个用来做验证的框架，它提供了很多现成方法来帮助开发实现表单验证功能。

## sinon

[sinon](http://sinonjs.org/)是一个mock工具。可以和各种测试框架配合使用。就像java里的[mockito](https://github.com/mockito/mockito)。

## eslint

[eslint](https://eslint.org/)是一个代码格式静态检查的工具。由于[ECMAScript](https://en.wikipedia.org/wiki/ECMAScript)和前端框架的发展，现在很多前端项目也有了编译过程，语法静态检查能够让一些打包发布时才能发现的问题在编码时就暴露出来。

## pug

[pug](https://pugjs.org/)是一个高性能的模板引擎。html的语法格式很严谨，但也很冗余。pug使用了一种简洁的语法来写html，用更少的代码传递更多的信息。

## stylus

[stylus](http://stylus-lang.com/)是一个css的预处理框架。其本质上做的事情与sass/less等类似。可以让开发用一种简洁的语法编写有逻辑的css。

## store

[store](https://www.npmjs.com/package/store)提供了友好的接口来操作浏览器的local storage。

## js-cookie

[js-cookie](https://www.npmjs.com/package/js-cookie)提供了友好的接口来操作cookies。

## babel

[babel](https://babeljs.io/)一个JavaScript编译器。Babel通过语法转换器支持最新版本的JavaScript。这些插件允许你立刻使用新语法，无需等待浏览器支持。

## webpack

[webpack](https://webpack.js.org/)是JavaScript应用程序的静态模块打包器(module bundler)。它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。

## echarts

[echarts](http://echarts.baidu.com/)是一个使用 JavaScript 实现的开源可视化库，可以流畅的运行在 PC 和移动设备上，兼容当前绝大部分浏览器（IE8/9/10/11，Chrome，Firefox，Safari等）。

# 其他一些我用过的前端技术

现在前端的技术真的可以用多如牛毛来形容，一些偶然的机会，我还用过一些前端技术罗列在下面。

## yeoman

[yeoman](http://yeoman.io/)是一个脚手架工具，帮助开发人员快速创建新项目。

## grunt/gulp

[grunt](https://gruntjs.com/)和[gulp](https://www.gulpjs.com/)是两个构建工具，可以定义任务，对前端项目中的js和css进行编译、压缩、合并等操作。有点像java中的[ant](http://ant.apache.org/)。如果用了webpack，其实就可以不用[grunt](https://gruntjs.com/)和[gulp](https://www.gulpjs.com/)了。


# 参考

* 各个技术的官方文档
* [Node.js 教程](http://www.runoob.com/nodejs/nodejs-tutorial.html)
* [入门Webpack](https://www.jianshu.com/p/42e11515c10f)
* [JavaScript自动化构建工具入门----grunt、gulp、webpack](http://www.cnblogs.com/weven/p/7544606.html)
* [Yeoman，教你快速打造自己的脚手架](https://www.jianshu.com/p/038c6b91f667)

日期:2018-08-08 作者:[辵鵵](https://about.me/qyf404)