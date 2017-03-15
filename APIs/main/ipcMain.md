# 7.ipcMain

> 原文：https://github.com/electron/electron/blob/master/docs/api/ipc-main.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 从主进程到渲染进程的异步通讯。

进程：[主进程](../../guides/glossary-of-terms.md#main-process)

`ipcMain`模块是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)类的实例。在主线程中使用时，它处理发送自渲染线程（网页）的异步和同步消息。发送自渲染器的消息将会使用这个模块中被分发。

## 发送消息

它也可以从主进程发送消息到渲染进程，更多信息请查看[webContents.send](./webContents.md#contents-send)。

 * 当发送一个消息，事件的名字是`channel`。
 * 回复一个同步消息，你需要设置`event.returnValue`。
 * 返回一个异步消息给发送者，你可能使用`event.sender.send(...)`。

一个在渲染进程和主进程之间发送和处理消息的例子：

    // 在主进程中。
    const {ipcMain} = require('electron')
    ipcMain.on('asynchronous-message', (event, arg) => {
        console.log(arg)  // 打印结果是"ping"
        event.sender.send('asynchronous-reply', 'pong')
    })

    ipcMain.on('synchronous-message', (event, arg) => {
        console.log(arg)  // 打印结果是"ping"
        event.returnValue = 'pong'
    })


    // 在渲染进程中（网页）。
    const {ipcRenderer} = require('electron')
    console.log(ipcRenderer.sendSync('synchronous-message', 'ping')) // 打印结果为"pong"

    ipcRenderer.on('asynchronous-reply', (event, arg) => {
        console.log(arg) // 打印结果为"pong"
    })
    ipcRenderer.send('asynchronous-message', 'ping')

## 方法

`ipcMain`模块又下面的监听事件的方法：

### `ipcMain.on(channel, listener)`

 * `channel` String类型
 * `listener` Function类型

监听`channel`，当一个新的消息送达监听器将会调用`listener(event, args...)`。

### `ipcMain.once(channel, listener)`

 * `channel` String类型
 * `listener` Function类型

给事件添加一个一次性`listener`函数。这个监听器只会在下一次一个消息被发送到`channel`时被调用，调用之后就会被移除。

### `ipcMain.removeListener(channel, listener)`

 * `channel` String类型
 * `listener` Function类型

通过`channel`在监听器数组中移除指定的`listener`。

### `ipcMain.removeAllListeners([channel])`

 * `channel` String类型（可选参数）

移除所有监听器，或者指定的`channel`中的所有监听器。

## Event对象

通过`callback`返回的`event`对象有下面的方法：

### `event.returnValue`

设置在一个同步消息中的返回值。

### `event.sender`

返回发送的消息的`webContents`，你可以调用`event.sender.send`来回复异步消息，查看[webContents.send](./webContents.md#contents-send)来获取更多消息。