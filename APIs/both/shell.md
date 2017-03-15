# 5.shell

> 原文：https://github.com/electron/electron/blob/master/docs/api/shell.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 使用文件和URL的默认应用管理文件和URL

进程：[主进程](../../guides/glossary-of-terms.md#main-process)，[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

`shell`模块提供有关于桌面集成的函数。

一个使用用户默认浏览器打开一个URL的例子：

    const {shell} = require('electron')

    shell.openExternal('https://github.com')

## 方法

`shell`模块有下面的方法：

### `shell.showItemInFolder(fullPath)`

 * `fullPath` String类型

返回值为`Boolean`类型 - 项是否展示成功。

在一个文件管理器中展示要展示的文件。如果可能，将会选中这个文件。

### `shell.openItem(fullPath)`

 * `fullPath` String类型

返回值为`Boolean`类型 - 项是否打开成功。

在桌面的默认方式中打开指定文件。

### `shell.openExternal(url[, options, callback])`

 * `url` String类型
 * `options` Object类型（可选参数）MacOS有效
     * `activate` Boolean类型 - `true`将把打开的应用放置在最前方，默认是`true`。
 * `callback` Function类型（可选参数）- 如果指定这个参数将会使用异步打开。*MacOS*
     * `error` Error类型

返回值为`Boolean`类型 - 应用程序是否有效的打开了URL。如果`callback`被指定，将总是返回`true`。

在桌面的默认打开方式中打开传入的外部协议URL。（例如，地址：用户的默认邮件代理的网址）。

### `shell.moveItemToTrash(fullPath)`

 * `fullPath` String类型

返回值为`Boolean`类型 - 项是否成功的被移除到了垃圾桶中。

移动给定的文件到垃圾桶，并且返回一个`boolean`类型的操作状态。

### `shell.beep()`

播放beep声音。

### `shell.writeShortcutLink(shortcutPath[, operation], options)` *Windows*

 * `shortcutPath` String类型
 * `operation` String类型（可选参数）- 默认是`create`，可以使用下面中的一个：
     * `create` - 创建一个新的快捷方式，如果需要将覆盖。
     * `update` - 在现有的快捷方式上只更新指定属性。
     * `replace` - 覆盖现有的快捷方式，如果快捷方式不存在将报错。
 * `options` [ShortcutDetails](https://github.com/electron/electron/blob/master/docs/api/structures/shortcut-details.md)类型

返回值为`Boolean`类型 - 快捷方式是否被创建成功。

创建或者更新`shortcutPath`下的一个快捷方式的链接

### `shell.readShortcutLink(shortcutPath)` *Windows*

 * `shortcutPath` String类型

返回值为[`ShortcutDetails`](https://github.com/electron/electron/blob/master/docs/api/structures/shortcut-details.md)类型

分解`shortcutPath`下的快捷方式的链接。

任何错误发生时都将会抛出一个意外。