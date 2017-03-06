# 10.调试主进程

> 原文：https://github.com/electron/electron/blob/master/docs/tutorial/debugging-main-process.md    
译者：[Lin](https://github.com/ShmilyLin)   

一个Electron浏览器窗口里面的DevTools仅可以调试窗口（例如，网页）中执行的JavaScript。为了调试在主线程中执行的JavaScript，你讲需要使用一个外部的调试器，并且使用`--debug`或`--debug-brk`开关来加载Electron。

<h2 id="command-line-switches">Command Line Switches</h2>

使用下面的一个命令行开关来启用主程序的调试：

<h3 id="debug-port"><code>--debug=[port]</code></h3>

Electron将会监听设置的`port`的端口号来获得V8的调试器协议消息，一个外部的调试器将需要连接到这个端口。默认`port`是`5858`。

    electron --debug=5858 your/app

<h3 id="debug-brk-port"><code>--debug-brk=[port]</code></h3>

类似于`--debug`，但是在JavaScript代码的第一行将会暂停执行。

<h2 id="external-debuggers">External Debuggers</h2>

你讲需要使用一个支持V8调试器协议的调试器，下面的指南将帮助你开始：

 * [Debugging the Main Process in VSCode](https://github.com/electron/electron/blob/master/docs/tutorial/debugging-main-process-vscode.md)
 * [Debugging the Main Process in node-inspector](https://github.com/electron/electron/blob/master/docs/tutorial/debugging-main-process-node-inspector.md)

