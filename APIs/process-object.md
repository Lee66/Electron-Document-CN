# 2.process对象

> 原文：https://github.com/electron/electron/blob/master/docs/api/process.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 扩展process对象

进程：[主进程](../guides/glossary-of-terms.md#main-process)，[渲染进程](../guides/glossary-of-terms.md#renderer-process)

Electron的`process`对象是从[Node.js的`process`对象](https://nodejs.org/api/process.html)扩展而来的。它添加了下面的事件，属性，和方法：

## 事件

### 事件: 'loaded'

当Electron已经加载它内部的初始化脚本并且已经开始加载网页或者主要脚本时被分发。

当node集成被关闭时，它可以被预加载脚本用来添加已经移除的Node全局标识回到全局作用域：

    // preload.js
    const _setImmediate = setImmediate
    const _clearImmediate = clearImmediate
    process.once('loaded', () => {
        global.setImmediate = _setImmediate
        global.clearImmediate = _clearImmediate
    })

## 属性

### `process.noAsar`

设置为`true`可以禁用Node的内置模块对`asar`文档的支持。

### `process.type`

当前进程的类型，可以是`"browser"`（即主进程）或`"renderer"`。

### `process.versions.electron`

Electron的版本字符串。

### `process.versions.chrome`

Chrome的版本字符串。

### `process.resourcesPath`

资源目录的路径。

### `process.mas`

对于Mac App Store版本，这个属性是`true`，其他版本是`undefined`。

### `process.windowsStore`

如果应用作为一个Windows Store应用（appx）运行，这个属性是`true`，否则为`undefined`。

### `process.defaultApp`

当应用被传入参数作为默认应用启动时, 在主进程中这个属性是`true`，否则是`undefined`。

## 方法

`process`对象有下面的方法：

### `process.crash()`

导致当前进程的主线程崩溃。

### `process.hang()`

导致当前进程的主线程挂起。

### `process.setFdLimit(maxDescriptors)`*MacOS,Linux*

 * `maxDescriptors` Integer类型

设置文件描述软限制为`maxDescriptors`或者系统硬限制，对当前进程来说以较低者为准。

### `process.getProcessMemoryInfo()`

返回值为`Object`类型：

 * `workingSetSize` Integer类型 - 当前被固定住的真实物理内存的最大的内存量。
 * `peakWorkingSetSize` Integer类型 - 永远被固定住的真实物理内存的最大的内存量。
 * `privateBytes` Integer类型 - 不被其他进程共享的内存量，例如JS堆或者HTML内容。
 * `sharedBytes` Integer类型 - 进程间共享的内存量，通常内存被Electron代码自己消耗。

返回一个包含了当前进程的内存使用统计的对象。请注意，所有统计都是使用千字节来报告的。

### `process.getSystemMemoryInfo()`

返回值为`Object`类型：

 * `total` Integer类型 - 以千字节为单位的系统的可用的物理内存总量。
 * `free` Integer类型 - 尚未被应用或者磁盘缓存使用的物理内存总量。
 * `swapTotal` Integer类型 - 以千字节为单位的系统的可用的交换内存总量。*Windows Linux*
 * `swapFree` Integer类型 - 以千字节为单位的系统的可用的交换内存自由量。*Windows Linux*

返回一个包含了整个系统的内存使用统计的对象。请注意，所有统计都是使用千字节来报告的。

