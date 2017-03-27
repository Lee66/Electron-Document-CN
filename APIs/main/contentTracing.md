# 4.contentTracing

> 原文：https://github.com/electron/electron/blob/master/docs/api/content-tracing.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 从Chromium的内容模块中收集跟踪数据来查找性能瓶颈并放缓操作。

进程：[主进程](../../guides/glossary-of-terms.html#main-process)

这个模块不包含一个网页界面，所以你需要在一个Chrome浏览器中打开`chrome://tracing/`并且加载生成的文件来展示结果。

**注意：**你不能在`app`模块分发`ready`事件之前使用这个模块。

```javascript
const {app, contentTracing} = require('electron')

app.on('ready', () => {
    const options = {
        categoryFilter: '*',
        traceOptions: 'record-until-full,enable-sampling'
    }

    contentTracing.startRecording(options, () => {
        console.log('Tracing started')

        setTimeout(() => {
            contentTracing.stopRecording('', (path) => {
                console.log('Tracing data recorded to ' + path)
            })
        }, 5000)
    })
})
```

## 方法

`contentTracing`模块有以下方法：

### `contentTracing.getCategories(callback)`

 * `callback` Function类型
     * `categories` String[]类型

得到一组category groups。category groups改变为新的编码路径被延伸／送达。

一旦所有的子进程都报告已经收到`getCategories`请求，`callback`将被使用一个category groups数组调用。

### `contentTracing.startRecording(options, callback)`

 * `options` Object类型
     * `categoryFilter` String类型
     * `traceOptions` String类型
 * `callback` Function类型

开始记录所有进程。

当子进程收到EnableRecording请求的时候立刻在本地开始记录。一旦所有进程都收到`startRecording`请求，`callback`将会被调用。

`categoryFilter`是一个过滤器来控制跟踪哪一个category groups。一个过滤器可以有一个可选的包含了一个匹配category的`-`前缀来排除category groups。不支持在同一个列表中同事包含和排除category patterns。

例如：

 * `test_MyTest*`，
 * `test_MyTest*,test_OtherStuff`，
 * `"-excluded_category1,-excluded_category2`

`traceOptions`控制什么样的跟踪是有效的，它是一个使用逗号分隔的列表。可能的可选值有：

 * `record-until-full`
 * `record-continuously`
 * `trace-to-console`
 * `enable-sampling`
 * `enable-systrace`

前三个可选值是跟踪记录模式，因此它们三个之间是不可同时使用的。如果有一个以上的踪记录模式出现在`traceOptions`字段中，最后一个将被优先使用。如果没有指定跟踪记录模式，则记录模式默认是`record-until-full`。

跟踪选项将在分析`traceOptions`并被应用之前首先被重置为默认选项（`record_mode`设置为`record-until-full`，`enable_sampling`和`enable_systrace`设置为`false`）。

### `contentTracing.stopRecording(resultFilePath, callback)`

 * `resultFilePath` String类型
 * `callback` Function类型
     * `resultFilePath` String类型

停止记录所有进程。

子进程通常会缓存跟踪数据并且很少刷新和发送跟踪数据回主进程。这有助于最小化跟踪时的运行时，因为使用进程间通讯（IPC）发送一个跟踪数据是一个开销非常大的操作。所以为了结束跟踪，我们需要异步告诉紫禁城来刷新和等待跟踪数据。

一旦所有进程都收到`stopRecording`请求，`callback`将会被使用一个包含了跟踪数据的文件调用。

跟踪数据不为空或者在一个临时文件中，则跟踪数据将会被写入到`resultFilePath`中。如果真实的文件路径不是`null`，那么将通过`callback`被返回。

### `contentTracing.startMonitoring(options, callback)`

 * `options` Object类型
     * `categoryFilter` String类型
     * `traceOptions` String类型
 * `callback` Function类型

开始监测所有进程。

当子进程收到`startMonitoring`请求的时候立刻在本地开始监测。

一旦所有子进程都收到`startMonitoring`请求，`callback`将会被调用。

### `contentTracing.stopMonitoring(callback)`

 * `callback` Function类型

停止监测所有进程。

一旦所有子进程都收到`stopMonitoring`请求，`callback`将会被调用。

### `contentTracing.captureMonitoringSnapshot(resultFilePath, callback)`

 * `resultFilePath` String类型
 * `callback` Function类型
     * `resultFilePath` String类型

获得当前监测追踪数据。

子进程通常会缓存跟踪数据并且很少刷新和发送跟踪数据回主进程。这是因为跟通过进程间通讯（IPC）发送踪数据可能是一个开销非常大的操作，我们要避免跟踪不必要的运行时开销。所以为了结束跟踪，我们需要异步告诉紫禁城来刷新和等待跟踪数据。

一旦所有进程都收到`stopRecording`请求，`callback`将会被使用一个包含了跟踪数据的文件调用。

### `contentTracing.getTraceBufferUsage(callback)`

 * `callback` Function
     * `value` Number
     * `percentage` Number

通过跟踪缓冲区进程来获得最大使用百分比的状态。当`TraceBufferUsage`的值被确定时`callback`会被调用。

