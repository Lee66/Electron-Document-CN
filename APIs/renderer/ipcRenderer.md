# 2.ipcRenderer

> 原文：https://github.com/electron/electron/blob/master/docs/api/ipc-renderer.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 从渲染进程到主进程的异步通讯。

进程：[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

`ipcRenderer`模块是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)类的实例。它提供少量的方法让你能够从渲染进程发送同步和一步的消息到主进程。你也可以收到主进程的回复。

代码例子请查看[ipcMain](../main/ipc-main.md)

## 方法

`ipcRenderer`模块有以下监听事件和发送消息的方法：

### `ipcRenderer.on(channel, listener)`

 * `channel` String类型
 * `listener` Function类型

监听`channel`，当一个新消息送达`listener`将会被调用`listener(event, args...)`。

### `ipcRenderer.once(channel, listener)`

 * `channel` String类型
 * `listener` Function类型

给事件添加一个一次性`listener`函数。这个`listener`只会在下一次一个消息送达`channel`时触发，触发后它就会被移除。

### `ipcRenderer.removeListener(channel, listener)`

 * `channel` String类型
 * `listener` Function类型

通过指定的`channel`移除监听器数组中指定的`listener`。

### `ipcRenderer.removeAllListeners([channel])`

 * `channel` String类型（可选参数）

移除所有监听起，或者`channel`中指定的那些监听器。

### `pcRenderer.send(channel[, arg1][, arg2][, ...])`

 * `channel` String类型
 * `...args` any[]类型

通过`channel`异步发送一个消息到主进程，你可以发送任意参数。参数将在JSON内部被联播，因此没有函数或者原型链被引用。

主进程通过`ipcMain`模块监听`channel`来处理它。

### `ipcRenderer.sendSync(channel[, arg1][, arg2][, ...])`

 * `channel` String类型
 * `...args` any[]类型

通过`channel`同步发送一个消息到主进程，你可以发送任意参数。参数将在JSON内部被联播，因此没有函数或者原型链被引用。

主进程通过`ipcMain`模块监听`channel`来处理它，并且通过设置`event.returnValue`来给予回复。

**注意：**发送一个同步消息将会阻塞整个渲染进程，除非你知道你正在做什么，否则你永远不要用到这个接口。

### `ipcRenderer.sendToHost(channel[, arg1][, arg2][, ...])`

 * `channel` String类型
 * `...args` any[]类型

同`ipcRenderer.send`一样，但是事件将会被送到主页的&lt;webview&gt;元素而不是送到主进程。
