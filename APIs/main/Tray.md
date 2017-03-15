# 16.Tray

> 原文：https://github.com/electron/electron/blob/master/docs/api/tray.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 添加图标和右键菜单到系统的通知区域

进程：[主进程](../../guides/glossary-of-terms.md#main-process)   

`Tray`是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)。

```javascript
const {app, Menu, Tray} = require('electron')

let tray = null
app.on('ready', () => {
tray = new Tray('/path/to/my/icon')
const contextMenu = Menu.buildFromTemplate([
    {label: 'Item1', type: 'radio'},
    {label: 'Item2', type: 'radio'},
    {label: 'Item3', type: 'radio', checked: true},
    {label: 'Item4', type: 'radio'}
])
tray.setToolTip('This is my application.')
tray.setContextMenu(contextMenu)
})
```

**平台的局限性：**

 * Linux中如果支持应用指示器那么应用指示器将被使用，否则将使用`GtkStatusIcon`代替。
 * Linux发行版中支持应用指示器，你需要安装`libappindicator1`来使tray图标工作。
 * 当有右键菜单时才会展示应用指示器。
 * 当Linux中的应用指示器被使用，`click`事件将被忽略。
 * Linux中为了使个别的`MenuItems`的改变生效，你需要再次调用`setContextMenu`。例如：


 ```javascript
const {app, Menu, Tray} = require('electron')

let appIcon = null
app.on('ready', () => {
    appIcon = new Tray('/path/to/my/icon')
    const contextMenu = Menu.buildFromTemplate([
        {label: 'Item1', type: 'radio'},
        {label: 'Item2', type: 'radio'}
    ])

    // Make a change to the context menu
    contextMenu.items[1].checked = false

    // Call this again for Linux because we modified the context menu
    appIcon.setContextMenu(contextMenu)
})
 ```

  * Windows中建议使用ICO格式的图标来获得最好的视觉效果。

如果你想在所有平台上保持完全相同的行为，你需要不依赖点击事件并且将右键菜单依附在托盘图标上。

### `new Tray(image)`

 * `image` ([NativeImage](../both/nativeImage.md) | String)类型

通过`image`创建一个托盘图标。

### 实例的事件

`Tray`模块分发以下事件：

#### 事件: 'click'

 * `event` Event类型
     * `altKey` Boolean类型
     * `shiftKey` Boolean类型
     * `ctrlKey` Boolean类型
     * `metaKey` Boolean类型
 * `bounds` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型 - 托盘图标的边界。

当托盘图片被点击的时候被分发。

#### 事件: 'right-click' *MacOS Windows*

 * `event` Event
     * `altKey` Boolean
     * `shiftKey` Boolean
     * `ctrlKey` Boolean
     * `metaKey` Boolean
 * `bounds` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型 - 托盘图标的边界。

当托盘图片被右键点击的时候被分发。

#### 事件: 'double-click' *MacOS Windows*

 * `event` Event
     * `altKey` Boolean
     * `shiftKey` Boolean
     * `ctrlKey` Boolean
     * `metaKey` Boolean
 * `bounds` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型 - 托盘图标的边界。

当托盘图片被双击的时候被分发。

#### 事件: 'balloon-show' *Windows*

当托盘气球展示的时候被分发。

#### 事件: 'balloon-click' *Windows*

当托盘气球被点击的时候被分发。

#### 事件: 'balloon-closed' *Windows*

当托盘气球因为超时关闭或者用户手动点击关闭时被分发。

#### 事件: 'drop' *MacOS*

任何被拖动的项目被拖动到托盘图标上的时候被分发。

#### 事件: 'drop-files' *MacOS*

 * `event` Event类型
 * `files` String[]类型 - 被拖动的文件的路径。

被拖动的文件拖动到托盘图标里面的时候被分发。

#### 事件: 'drop-text' *MacOS*

 * `event` Event类型
 * `text` String类型 - 被拖动的文本字符串。

被拖动的文本拖动到托盘图标里面的时候被分发。

#### 事件: 'drag-enter' *MacOS*

当一个拖动操作进入到托盘图标的时候被分发。

#### 事件: 'drag-leave' *MacOS*

当一个拖动操作离开托盘图标的时候被分发。

#### 事件: 'drag-end' *MacOS*

当拖动操作在托盘图标上释放或在其他的位置释放时被分发。

### 实例的方法

`Tray`类有以下方法：

#### `tray.destroy()`

立刻销毁托盘图标。

#### `tray.setImage(image)`

 * `image` ([NativeImage](../both/nativeImage.md) | String)类型

设置托盘图标。

#### `tray.setPressedImage(image)` *MacOS*

 * `image` [NativeImage](../both/nativeImage.md)类型

设置MacOS下按下托盘图标是的托盘图标。

#### `tray.setToolTip(toolTip)`

 * `toolTip` String类型

设置托盘图标的悬停文本。

#### `tray.setTitle(title)` *MacOS*

 * `title` String类型

设置显示在状态栏中托盘图标旁边的文字。

#### `tray.setHighlightMode(mode)` *MacOS*

 * `mode` String类型 - 高亮模式有以下值类型：
     * `selection` - 托盘图标被点击的时候和右键菜单打开的时候高亮。这个是默认值。
     * `always` - 托盘图标维持高亮状体。
     * `never` - 托盘图标没有高亮状态。

设置什么时候托盘的图标背景变成高亮状态（蓝色）。

**注意：**当窗口是否可见状态改变的时候，你可以使用[`BrowserWindow`]()的`highlightMode`在`'never'`和`'always'`模式指尖切换。

```javascript
const {BrowserWindow, Tray} = require('electron')

const win = new BrowserWindow({width: 800, height: 600})
const tray = new Tray('/path/to/my/icon')

tray.on('click', () => {
    win.isVisible() ? win.hide() : win.show()
})
win.on('show', () => {
    tray.setHighlightMode('always')
})
win.on('hide', () => {
    tray.setHighlightMode('never')
})
```

#### `tray.displayBalloon(options)` *Windows*

 * `options` Object类型
     * `icon` (NativeImage | String)类型（可选参数）
     * `title` String类型（可选参数）
     * `content` String类型（可选参数）

展示一个托盘气球。

#### `tray.popUpContextMenu([menu, position])` *MacOS Windows*

 * `menu` Menu类型（可选参数）
 * `position` Object类型（可选参数）- 弹出的位置。
     * `x` Integer
     * `y` Integer

弹出一个托盘图标的右键菜单。当`menu`参数被设置，`menu`将被展示代替托盘图标的右键菜单。

`position`只在Windows中有效，并且默认值是`(0, 0)`。

#### `tray.setContextMenu(menu)`

 * `menu` Menu类型

给这个托盘图标设置右键菜单。

#### `tray.getBounds()` *MacOS Windows*

返回值为[`Rectangle`](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型

作为`Object`的托盘图标的`bounds`。

#### `tray.isDestroyed()`

返回值为`Boolean`类型 - 托盘图标是否被销毁。

