# 15.systemPreferences

> 原文：https://github.com/electron/electron/blob/master/docs/api/system-preferences.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 获取系统偏好设置

进程：[主进程](../../guides/glossary-of-terms.md#main-process)   

```javascript
const {systemPreferences} = require('electron')
console.log(systemPreferences.isDarkMode())
```

## 事件

`systemPreferences`对象分发下面事件：

### 事件: 'accent-color-changed' *Windows*

返回值：

 * `event` Event类型
 * `newColor` String类型 - 用户指定作为他们系统颜色的新的RGBA格式的颜色。

### 事件: 'color-changed' *Windows*

返回值：

 * `event` Event类型

### 事件: 'inverted-color-scheme-changed' *Windows*

返回值： 

 * `event` Event类型
  * `invertedColorScheme` Boolean类型 - 如果是反向配色方案，例如一个高对比度的主题将要被使用，则这个值为`true`。否则这个值为`false`。

## 方法

### `systemPreferences.isDarkMode()` *MacOS*

返回值为`Boolean`类型 - 系统是否是深色模式。

### `systemPreferences.isSwipeTrackingFromScrollEventsEnabled()` *MacOS*

返回值为`Boolean`类型 - 是否开启页面之间的切换。

### `systemPreferences.postNotification(event, userInfo)` *MacOS*

 * `event` String类型
 * `userInfo` Object类型

将`event`作为MacOS的本地通知发送。`userInfo`是一个和通知一起发送的包含了用户信息字典的对象。

### `systemPreferences.postLocalNotification(event, userInfo)` *MacOS*

 * `event` String类型
 * `userInfo` Object类型

将`event`作为MacOS的本地通知发送。`userInfo`是一个和通知一起发送的包含了用户信息字典的对象。

### `systemPreferences.subscribeNotification(event, callback)` *MacOS*

 * `event` String类型
 * `callback` Function类型
     * `event` String类型
     * `userInfo` Object类型

订阅MacOS的本地通知，当对应的`event`发生的时候`callback`将会使用`callback(event, userInfo)`被调用。`userInfo`是一个和通知一起发送的包含了用户信息字典的对象。

返回订阅器的`id`，可以用来取消订阅`event`。

这个接口的本质是订阅`NSDistributedNotificationCenter`，例如`event`的值可以是：

 * `AppleInterfaceThemeChangedNotification`
 * `AppleAquaColorVariantChanged`
 * `AppleColorPreferencesChangedNotification`
 * `AppleShowScrollBarsSettingChanged`

### `systemPreferences.unsubscribeNotification(id)` *MacOS*

 * `id` Integer类型

通过`id`移除订阅器。

### `systemPreferences.subscribeLocalNotification(event, callback)` *MacOS*

 * `event` String类型
 * `callback` Function类型
     * `event` String类型
     * `userInfo` Object类型

同`subscribeNotification`一样，不过本地是默认使用`NSNotificationCenter`。这个对于事件是必要的，例如`NSUserDefaultsDidChangeNotification`

### `systemPreferences.unsubscribeLocalNotification(id)` *MacOS*

 * `id` Integer类型

同`unsubscribeNotification`一样，不过是从`NSNotificationCenter`中移除订阅器。

### `systemPreferences.getUserDefault(key, type)` *MacOS*

 * `key` String类型
 * `type` String类型 - 可以是`string`、`boolean`、`integer`、`float`、`double`、`url`、`array`、`dictionary`

通过`key`来获取系统偏好设置里面对应的值。

这个接口在MacOS中使用`NSUserDefaults`。一些常用的`key`和`type`有：

 * `AppleInterfaceStyle`: `string`
 * `AppleAquaColorVariant`: `integer`
 * `AppleHighlightColor`: `string`
 * `AppleShowScrollBars`: `string`
 * `NSNavRecentPlaces`: `array`
 * `NSPreferredWebServices`: `dictionary`
 * `NSUserDictionaryReplacementItems`: `array`


### `systemPreferences.setUserDefault(key, type, value)` *MacOS*

 * `key` String类型
 * `type` String类型 - 查看[getUserDefault](#systempreferencesgetuserdefaultkey-type-macos)
 * `value` String类型

通过`key`来设置系统偏好设置里面对应的值。

请注意，`type`应该和`value`的实际类型匹配。如果不匹配将会抛出一个错误。An exception is thrown if they don't.

这个接口在MacOS中使用`NSUserDefaults`。一些常用的`key`和`type`有：

 * `ApplePressAndHoldEnabled`: `boolean`

### `systemPreferences.isAeroGlassEnabled()` *Windows*

如果[DWM composition](https://msdn.microsoft.com/en-us/library/windows/desktop/aa969540.aspx)（毛玻璃效果）被激活，那么这个方法将返回`true`，否则将返回`false`。

一个使用这个接口的例子，来决定是否你需要创建一个透明窗口（当`DWM composition`被禁用的时候透明窗口将不能正常的工作）：

```javascript
const {BrowserWindow, systemPreferences} = require('electron')
let browserOptions = {width: 1000, height: 800}

// Make the window transparent only if the platform supports it.
if (process.platform !== 'win32' || systemPreferences.isAeroGlassEnabled()) {
    browserOptions.transparent = true
    browserOptions.frame = false
}

// Create the window.
let win = new BrowserWindow(browserOptions)

// Navigate.
if (browserOptions.transparent) {
    win.loadURL(`file://${__dirname}/index.html`)
} else {
    // No transparency, so we load a fallback that uses basic styles.
    win.loadURL(`file://${__dirname}/fallback.html`)
}
```

### `systemPreferences.getAccentColor()` *Windows*

返回值为`String`类型 - 十六进制RGBA格式的用户当前系统颜色偏好。

```javascript
const color = systemPreferences.getAccentColor() // `"aabbccdd"`
const red = color.substr(0, 2) // "aa"
const green = color.substr(2, 2) // "bb"
const blue = color.substr(4, 2) // "cc"
const alpha = color.substr(6, 2) // "dd"
```

### `systemPreferences.getColor(color)` *Windows*

 * `color` String类型 - 使用以下值之一：
     * `3d-dark-shadow` - 显示的3D元素的深色阴影颜色。
     * `3d-face` - 显示的3D元素和对话框背景的表面颜色。
     * `3d-highlight` - 显示的3D元素的高亮颜色。
     * `3d-light` - 显示的3D元素的光亮的颜色。
     * `3d-shadow` - 显示的3D元素的阴影的颜色。
     * `active-border` - 活动窗口的边框的颜色。
     * `active-caption` - 活动窗口的标题栏。如果开启了颜色梯度变化，那么这个值是活动窗口标题栏最左侧的颜色。
     * `active-caption-gradient` - 活动窗口标题栏最右侧的颜色。
     * `app-workspace` - 多文档界面（MDI）应用的背景颜色。
     * `button-text` - 推送按钮的文字的颜色。
     * `caption-text` - 标题栏的文字、缩放框和滚动条剪头框的颜色。
     * `desktop` - 桌面背景颜色。
     * `disabled-text` - 灰色（无效的）文本的颜色。
     * `highlight` - 控件中选中项的颜色。
     * `highlight-text` - 控件中选中项的文本的颜色。
     * `hotlight` - 超链接或热跟踪项的颜色。
     * `inactive-border` - 非活动窗口边框的颜色。
     * `inactive-caption` - 非活动窗口的标题栏的颜色。如果开启了颜色梯度变化，那么这个值是活动窗口标题栏最左侧的颜色。
     * `inactive-caption-gradient` - 非活动窗口标题栏最右侧的颜色。
     * `inactive-caption-text` - 非活动标题栏的文字的颜色。
     * `info-background` - 工具提示控件的背景颜色。
     * `info-text` - 工具提示控件的文本颜色。
     * `menu` - 菜单背景颜色。
     * `menu-highlight` - 菜单栏为平面菜单时的菜单项的高亮颜色。
     * `menubar` - 菜单栏为平面菜单时的背景颜色。
     * `menu-text` - 菜单的文字颜色。
     * `scrollbar` - 滚动条灰色区域的颜色。
     * `window` - 窗口背景色。
     * `window-frame` - 窗口框架颜色。
     * `window-text` - 窗口内的文本颜色。

返回值为`String`类型 - 十六进制的RGB格式（`#ABCDEF`）的系统颜色设置。查看[Windows docs](https://msdn.microsoft.com/en-us/library/windows/desktop/ms724371(v=vs.85).aspx)获取更详细信息。

### `systemPreferences.isInvertedColorScheme()` *Windows*

返回值为`Boolean`类型 - 如果是反向配色方案，例如一个高对比度的主题将要被使用，则这个值为`true`。否则这个值为`false`。
