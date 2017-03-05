# 4.REPL

> 原文：https://github.com/electron/electron/blob/master/docs/tutorial/repl.md   
译者：[Lin](https://github.com/ShmilyLin)   


Read-Eval-Print-Loop (REPL)是一个简单的计算机编程环境，可以提供单个用户的输入，对其求值，并且给用户返回计算结果。   

`repl`模块提供一个可以访问使用的REPL实现：   

 * 如果你有安装`electron`或者`electron-prebuilt`作为本地项目依赖：   

    ./node_modules/.bin/electron --interactive

 * 如果你有安装`electron`或者`electron-prebuilt`到全局：   

    electron --interactive

这只是对主进程创建了一个REPL。你可以使用开发工具中的控制台来得到渲染进程的REPL。   

注意：`electron --interactive`在Windows上是无效的。   

更多信息可以在[Node.js REPL docs](https://nodejs.org/dist/latest/docs/api/repl.html)中找到。   