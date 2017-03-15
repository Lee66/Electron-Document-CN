# 9.MenuItem

> 原文：https://github.com/electron/electron/blob/master/docs/api/menu-item.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 添加项到原生应用菜单和右键菜单

进程：[主进程](../../guides/glossary-of-terms.md#main-process)

查看[`Menu`](./Menu.md)的例子。

### `new MenuItem(options)`

 * `options` Object类型
     * `click` Function类型（可选参数）- 当菜单项被点击的时候将被使用`click(menuItem, browserWindow, event)`调用。
         * `menuItem` MenuItem类型
         * `browserWindow` BrowserWindow类型
         * `event` Event类型
     * `role` String类型（可选参数）- 菜单项的默认动作，当指定`click`属性后这个属性被忽略。
     * `type` String类型（可选参数）- 可以是`normal`、`separator`、`submenu`、`checkbox`或者`radio`。
     * `label` String类型（可选参数）
     * `sublabel` String类型（可选参数）
     * `accelerator` [Accelerator](https://github.com/electron/electron/blob/master/docs/api/accelerator.md)类型（可选参数）
     * `icon` ([NativeImage](./both/nativeImage.md) | String)类型（可选参数）
     * `enabled` Boolean类型（可选参数）- 如果设置为`false`，菜单项将变为灰色并且无法点击。
     * `visible` Boolean类型（可选参数）- 如果设置为`false`，菜单项将被完全隐藏。
     * `checked` Boolean类型（可选参数）- 只有菜单项被指定为`checkbox`或者`radio`类型时有效。
     * `submenu` (MenuItemConstructorOptions[] | Menu)类型（可选参数）- 菜单项需要被指定为`submenu`类型。如果`submenu`被设置，`type: 'submenu'`可以被省略。如果值不是一个`Menu`类型，那么它将被使用`Menu.buildFromTemplate`自动的转变成一个`Menu`类型。
     * `id` String类型（可选参数）- 一个单独的菜单内唯一的值。如果定义这个值，那么它可以被`position`参数用来指向这个项。
     * `position` String类型（可选参数）- 这个字段允许在给定的菜单内详细的指定项的位置。

最好对任意一个菜单项都指定可以匹配标准作用的`role`，而不是试图手动实现一个`click`函数的执行内容。内置的`role`行为将会给予最好的原生体验。

当使用`role`时`label`和`accelerator`是可选的，并且将默认为每个平台适当的值。

`role`属性可以有以下值：

 * `undo`
 * `redo`
 * `cut`
 * `copy`
 * `paste`
 * `pasteandmatchstyle`
 * `selectall`
 * `delete`
 * `minimize` - 最小化当前窗口。
 * `close` - 关闭当前窗口。
 * `quit`- 退出应用。
 * `reload` - 重载当前窗口。
 * `forcereload` - 忽视缓存重载当前窗口。
 * `toggledevtools` - 在当前窗口中切换开发工具。
 * `togglefullscreen` - 在当前窗口中切换全屏模式
 * `resetzoom` - 将聚焦的页面的缩放等级重置为原来的大小。
 * `zoomin` - 放大聚焦的页面10%
 * `zoomout` - 缩小聚焦的页面10%

MacOS中`role`也可以有以下附加值：

 * `about` - 映射到`orderFrontStandardAboutPanel`动作。
 * `hide` - 映射到`hide`动作。
 * `hideothers` - 映射到`hideOtherApplications`动作。
 * `unhide` - 映射到`unhideAllApplications`动作。
 * `startspeaking` - 映射到`startSpeaking`动作。
 * `stopspeaking` - 映射到`stopSpeakingv
 * `front` - 映射到`arrangeInFront`动作。
 * `zoom` - 映射到`performZoom`动作。
 * `window` - 子菜单是一个`"Window"`菜单。
 * `help` - 子菜单是一个`"Help"`菜单。
 * `services` - 子菜单是一个`"Services"`菜单。

当在MacOS中指定`role`，只有`label`和`accelerator`是可以影响到`MenuItem`的选项。其他所有的选项都将被忽略。

### 实例的属性

下面的属性在`MenuItem`的实例中可用：

#### `menuItem.enabled`

一个`Boolean`类型的标志，标示项是否是可用的，这个属性可以被动态的改变。

#### `menuItem.visible`

一个`Boolean`类型的标志，标示项是否是可见的，这个属性可以被动态的改变。

#### `menuItem.checked`

一个`Boolean`类型的标志，标示项是否是可点击的，这个属性可以被动态的改变。

`checkbox`菜单项将在选中的时候切换`checked`属性为`on`或者`off`.

`radio`菜单项当它是被点击的时候，将打开它自己的`checked`属性，并且将关闭同菜单下的所有相邻项的这个属性。

你可以添加一个附加行为的`click`函数。

#### `menuItem.label`

`String`类型，菜单项可见标签的代表。

#### `menuItem.click`

当`MenuItem`接收到一个点击事件时被激发的函数。