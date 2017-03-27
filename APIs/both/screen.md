# 4.screen

> 原文：https://github.com/electron/electron/blob/master/docs/api/screen.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 检索关于屏幕大小、显示器、光标位置等信息。

进程：[主进程](../../guides/glossary-of-terms.md#main-process)，[渲染进程](../../guides/glossary-of-terms.md#renderer-process)‘

你不可以在`app`模块分发`ready`事件之前引用或使用这个模块。

`screen`是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)。

**注意：**在渲染器或开发工具中，`window.screen`是一个被存储着的DOM属性，所以写`let {screen} = require('electron')`是无效的。

创建一个充满整个屏幕的窗口的例子：

```javascript
const electron = require('electron')
const {app, BrowserWindow} = electron

let win

app.on('ready', () => {
    const {width, height} = electron.screen.getPrimaryDisplay().workAreaSize
    win = new BrowserWindow({width, height})
    win.loadURL('https://github.com')
})
```

在外接显示器中创建一个窗口的例子：

```javascript
const electron = require('electron')
const {app, BrowserWindow} = require('electron')

let win

app.on('ready', () => {
    let displays = electron.screen.getAllDisplays()
    let externalDisplay = displays.find((display) => {
        return display.bounds.x !== 0 || display.bounds.y !== 0
    })

    if (externalDisplay) {
        win = new BrowserWindow({
        x: externalDisplay.bounds.x + 50,
        y: externalDisplay.bounds.y + 50
        })
        win.loadURL('https://github.com')
    }
})
```

## 事件

`screen`模块会分发以下事件：

### 事件: 'display-added'

返回值为：

 * `event` Event类型
 * `newDisplay` [Display](https://github.com/electron/electron/blob/master/docs/api/structures/display.md)类型

当`newDisplay`已经被添加的时候分发这个事件。

### 事件: 'display-removed'

返回值为：

 * `event` Event类型
 * `oldDisplay` [Display](https://github.com/electron/electron/blob/master/docs/api/structures/display.md)类型

当`oldDisplay`已经被移除的时候分发这个事件。

### 事件: 'display-metrics-changed'

返回值为：

 * `event` Event类型
 * `display` [Display](https://github.com/electron/electron/blob/master/docs/api/structures/display.md)类型
 * `changedMetrics` String[]类型

当在一个`display`钟一个或多个度量改变的时候这个事件被分发。`changedMetrics`是一个描述变化的字符串组成的数组。可能的改变有`bounds`、`workArea`、`scaleFactor`和`rotation`。

## 方法

`screen`模块有以下方法：

### `screen.getCursorScreenPoint()`

返回值为`Object`类型：

 * `x` Integer类型
 * `y` Integer类型

鼠标指针的当前绝对位置。

### `screen.getPrimaryDisplay()`

返回值为[`Display`](https://github.com/electron/electron/blob/master/docs/api/structures/display.md)类型 - 主显示器。

### `screen.getAllDisplays()`

返回值为[`Display[]`](https://github.com/electron/electron/blob/master/docs/api/structures/display.md)类型 - 目前可用的所有显示器组成的数组。

### `screen.getDisplayNearestPoint(point)`

 * `point` Object类型
     * `x` Integer类型
     * `y` Integer类型

返回值为[`Display`](https://github.com/electron/electron/blob/master/docs/api/structures/display.md)类型 - 最接近指定点的显示器。

### `screen.getDisplayMatching(rect)`

 * `rect` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型

返回值为[`Display`](https://github.com/electron/electron/blob/master/docs/api/structures/display.md) - 最靠近指定位置范围的显示器。
