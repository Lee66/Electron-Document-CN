# 3.remote

> 原文：https://github.com/electron/electron/blob/master/docs/api/remote.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 从渲染进程使用主进程模块

进程：[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

`remote`模块提供一个在渲染进程（网页）进程和主进程之间做进程通讯（IPC）的简单方法。

Electron中，GUI相关的模块（例如`dialog`，`menu`等）只在主进程中有效，在渲染进程中无效。未了在渲染进程中使用它们，`ipc`模块必须发送进程间的信息给主进程。有了`remote`模块，你可以调用主进程袖低昂的方法而不用明确的发送进程间信息，类似于Java的[RMI](http://en.wikipedia.org/wiki/Java_remote_method_invocation)。一个在渲染进程中跟创建一个浏览器窗口的例子：

```javascript
const {BrowserWindow} = require('electron').remote
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('https://github.com')
```

**注意：**想要反向调用（从主进程访问渲染进程），你可以使用[webContents.executeJavascript](../main/webContents.md#contentsexecutejavascriptcode-usergesture-callback)。

## 远程对象

通过`remote`模块返回的每个对象（包括函数）都代表着一个主进程（我们调用它来调用一个远程对象或远程函数）中的对象。当你调用一个远程对象的方法、调用一个远程函数，或者通过远程构造器（函数）创建一个新的对象时，你将发送一个同步的进程间消息。

上面的例子中，`BrowserWindow`和`win`都是远程对象，并且`new BrowserWindow`没有在渲染进程中创建一个`BrowserWindow`对象。相反，他在主进程中创建了一个`BrowserWindow`对象，并且将这个对应的远程对象返回到渲染线程中，即`win`对象。

**注意：**当远程对象第一次通过`remote`引用过来时只是当前的[enumerable properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties) 。

**注意：**所有通过`remote`模块获取的数组和数据是通过进程间通讯复制过来的A。在渲染进程中修改它们并不会同时在主进程中修改，反过来也一样。

## 远程对象的生命周期

Electron确保在渲染进程中对应的这个远程对象会一直存在（换句话说，没有垃圾回收机制），对应的主进程中的对象也不会被回收。当远程模块已经被回收，主进程中对应的对象将被销毁。

如果远程对象在渲染进程中内存泄漏（例如存储在一个映射中没有被释放），对应的主进程中的对象也会造成内存泄漏，所以你需要非常小心的防止远程对象发生内存泄漏。

主要的值类型，例如`strings`和`numbers`，都是通过拷贝的方式发送的。

## 回调到主线程

主进程中的代码可以接收渲染器的回调——来自于`remote`模块的实例——但是当你使用这个功能室必须非常的小心。

首先，为了避免死锁，回调到主进程是同步调用。你不应该期望主进程能够通过回调得到返回值。

比如你不能给主进程中调用的`Array.map`中传递一个渲染进程中的函数：

```javascript
// main process mapNumbers.js
exports.withRendererCallback = (mapper) => {
    return [1, 2, 3].map(mapper)
}

exports.withLocalCallback = () => {
    return [1, 2, 3].map(x => x + 1)
}
```


```javascript
// renderer process
const mapNumbers = require('electron').remote.require('./mapNumbers')
const withRendererCb = mapNumbers.withRendererCallback(x => x + 1)
const withLocalCb = mapNumbers.withLocalCallback()

console.log(withRendererCb, withLocalCb)
// [undefined, undefined, undefined], [2, 3, 4]
```

正如你看到的，渲染器并没有同步回调预期的返回值，并没有匹配到主进程中存在的相同的回掉的返回值。

其次，主进程的回调将会一直持续到它们被回收掉。

例如，下面的代码第一眼看起来是没有问题的。它给一个远程对象上的`close`事件安装了一个回调：

```javascript
require('electron').remote.getCurrentWindow().on('close', () => {
  // window was closed...
})
```

但是记住，这个回调是引用自主进程，除非你显式的写在它。如果你没有显式的卸载它，每当你重新加载你的窗口这个回调都会被再一次安装，每次重启都会泄漏一个回调。

更糟糕的是，由于以前安装的回调的上下文已经被释放掉，当主进程的`close`事件被分发时将会引起例外。

为了避免这个问题，请确保你清理所有回调到主进程的渲染器回调。这包括清理事件处理器，或者明确的告诉主进程来自渲染进程的回调已经被销毁。

## 访问主进程的内置模块

主进程的内置模块被作为属性添加到`remote`模块中，所以你可以像使用electron模块一样直接使用它们。

```javascript
const app = require('electron').remote.app
console.log(app)
```

## 方法

`remote`模块有以下方法：

#### `remote.require(module)`

 * `module` String类型

返回任意类型 - 主进程中通过`require(module)`返回的对象。

#### `remote.getCurrentWindow()`

返回值为[`BrowserWindow`](../main/BrowserWindow.md)类型 - 这个页面所在的窗口。

#### `remote.getCurrentWebContents()`

返回值为[`WebContents`](../main/webContents.md) - 这个网页的网络内容。

#### `remote.getGlobal(name)`

 * `name` String类型

返回任意类型 - 主进程中`name`对应的全局变量（例如`global[name]`）。

## 属性

### `remote.process`

主进程的`process`对象。这个方法类似于`remote.getGlobal('process')`但是会被缓存。
