# 8.Menu

> 原文：https://github.com/electron/electron/blob/master/docs/api/menu.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 创建原生应用菜单和右键菜单

进程：[主进程](../../guides/glossary-of-terms.md#main-process)

### `new Menu()`

创建一个新的菜单。

### 静态方法

`menu`类有以下静态方法：

#### `Menu.setApplicationMenu(menu)`

 * `menu` Menu类型

MacOS中设置`menu`作为应用菜单。Windows和Linux中，`menu`将被设置为每个窗口的顶部菜单。

**注意：**这个接口必须在`app`模块的`ready`事件之后调用。

#### `Menu.getApplicationMenu()`

返回值为`Menu`类型 - 如果设置了应用的菜单则返回应用的菜单，如果没有设置则返回`null`。

#### `Menu.sendActionToFirstResponder(action)` *MacOS*

 * `action` String类型

将`action`发送给应用的第一响应器。这将被用来模仿默认的Cocoa菜单行为，通常你只使用`MenuItem`的`role`属性。

查看[macOS Cocoa Event Handling Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/EventArchitecture.html#//apple_ref/doc/uid/10000060i-CH3-SW7)来获取更多关于MacOS的原生actions的信息。

#### `Menu.buildFromTemplate(template)`

 * `template` MenuItemConstructorOptions[]类型

返回值为`Menu`类型

一般情况下，`template`只是一个构成一个[MenuItem](./MenuItem.md)的`options`组成的数组。用法可以参考上面。

你也可以将其他字段添加到`template`的元素中，然后它们将成为构成菜单项的属性。

## 实例的方法

`menu`对象有以下实例方法：

### `menu.popup([browserWindow, options])`

 * `browserWindow` BrowserWindow类型（可选参数）- 默认是聚焦的窗口。
 * `options` Object类型（可选参数）
     * `x` Number类型（可选参数）- 默认是当前鼠标光标的位置
     * `y` Number类型 (如果设置了`x`则必须设置这个选项) - 默认是当前鼠标光标的位置。
     * `async` Boolean类型（可选参数）- 设置为`true`则这个方法的返回值立刻被调用，设置为`false`则在菜单被选择或关闭后调用返回值。默认为`false`。
     * `positioningItem` Number类型（可选参数）*MacOS* - 被放置在鼠标光标下指定坐标的菜单项的索引。默认为`-1`。

在`browserWindow`中作为右键菜单弹出这个菜单。

### `menu.closePopup([browserWindow])`

 * `browserWindow` BrowserWindow类型（可选参数）- 默认是聚焦的窗口。

在`browserWindow`中关闭右键菜单。

### `menu.append(menuItem)`

 * `menuItem` MenuItem类型

添加菜单项到菜单中。

### `menu.insert(pos, menuItem)`

 * `pos` Integer类型
 * `menuItem` MenuItem类型

插入`menuItem`到菜单的`pos`位置。

### 实例的属性

`menu`对象有以下属性：

#### `menu.items`

一个包含了菜单的项的`MenuItem[]`类型数组。

每个`Menu`包含了多个[`MenuItem`](./MenuItem.md)，并且每个`MenuItem`可以又一个子菜单。

## 例子

`Menu`类只在主进程中有效，但是你也可以在渲染线程中通过[`remote`](../renderer/remote.md)模块来使用它。

    const {app, Menu} = require('electron')

    const template = [
    {
        label: 'Edit',
        submenu: [
        {
            role: 'undo'
        },
        {
            role: 'redo'
        },
        {
            type: 'separator'
        },
        {
            role: 'cut'
        },
        {
            role: 'copy'
        },
        {
            role: 'paste'
        },
        {
            role: 'pasteandmatchstyle'
        },
        {
            role: 'delete'
        },
        {
            role: 'selectall'
        }
        ]
    },
    {
        label: 'View',
        submenu: [
        {
            role: 'reload'
        },
        {
            role: 'forcereload'
        },
        {
            role: 'toggledevtools'
        },
        {
            type: 'separator'
        },
        {
            role: 'resetzoom'
        },
        {
            role: 'zoomin'
        },
        {
            role: 'zoomout'
        },
        {
            type: 'separator'
        },
        {
            role: 'togglefullscreen'
        }
        ]
    },
    {
        role: 'window',
        submenu: [
        {
            role: 'minimize'
        },
        {
            role: 'close'
        }
        ]
    },
    {
        role: 'help',
        submenu: [
        {
            label: 'Learn More',
            click () { require('electron').shell.openExternal('https://electron.atom.io') }
        }
        ]
    }
    ]

    if (process.platform === 'darwin') {
    template.unshift({
        label: app.getName(),
        submenu: [
        {
            role: 'about'
        },
        {
            type: 'separator'
        },
        {
            role: 'services',
            submenu: []
        },
        {
            type: 'separator'
        },
        {
            role: 'hide'
        },
        {
            role: 'hideothers'
        },
        {
            role: 'unhide'
        },
        {
            type: 'separator'
        },
        {
            role: 'quit'
        }
        ]
    })
    // Edit menu.
    template[1].submenu.push(
        {
        type: 'separator'
        },
        {
        label: 'Speech',
        submenu: [
            {
            role: 'startspeaking'
            },
            {
            role: 'stopspeaking'
            }
        ]
        }
    )
    // Window menu.
    template[3].submenu = [
        {
        label: 'Close',
        accelerator: 'CmdOrCtrl+W',
        role: 'close'
        },
        {
        label: 'Minimize',
        accelerator: 'CmdOrCtrl+M',
        role: 'minimize'
        },
        {
        label: 'Zoom',
        role: 'zoom'
        },
        {
        type: 'separator'
        },
        {
        label: 'Bring All to Front',
        role: 'front'
        }
    ]
    }

    const menu = Menu.buildFromTemplate(template)
    Menu.setApplicationMenu(menu)

### 渲染进程

下面的例子是在网页（渲染进程）中使用`remote`模块来动态的创建一个菜单，并且当用户右键点击页面时展示它：

    <!-- index.html -->
    <script>
    const {remote} = require('electron')
    const {Menu, MenuItem} = remote

    const menu = new Menu()
    menu.append(new MenuItem({label: 'MenuItem1', click() { console.log('item 1 clicked') }}))
    menu.append(new MenuItem({type: 'separator'}))
    menu.append(new MenuItem({label: 'MenuItem2', type: 'checkbox', checked: true}))

    window.addEventListener('contextmenu', (e) => {
    e.preventDefault()
    menu.popup(remote.getCurrentWindow())
    }, false)
    </script>

## MacOS应用菜单的注意事项

MacOS有一个和Windows以及Linux完全不同的风格。这里有一些可以使你的应用的菜单更像原生菜单的注意事项。

### 标准菜单

MacOS中有许多系统定义的标准菜单，比如说`Services（服务）`和`Windows（窗口）`菜单。要使你的菜单变成一个标准菜单，你需要设置你的菜单的`role`为下面的值中的一个，然后Electron将识别它们并且将它们设置为标准菜单：

 * `window`
 * `help`
 * `services`

 ### 标准菜单的动作

MacOS有给一些菜单项提供标准动作，例如`About xxx`、`Hide xxx`和`Hide Others`。要给一个菜单项的动作设置为标准动作，你需要设置菜单项的`role`参数。

### 主菜单的名字

MacOS中，无论你将应用菜单的第一项的标签设置成什么，它总是你应用的名字。想要改变它，需要改变你应用包中的`Info.plist`文件。查看[About Information Property List Files](https://developer.apple.com/library/ios/documentation/general/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html)来获取更多信息。

## 给指定的浏览器窗口设置菜单 (Linux Windows)

浏览器窗口的[`setMenu`方法]()可以设置为某一浏览器的菜单。

## 菜单项的位置

当使用`Menu.buildFromTemplate`创建一个菜单时，你可以利用`position`和`id`来控制菜单项如何放置。

`MenuItem`的`position`参数有`[placement]=[id]`形式，`placement`是`before`、`after`或`endof`其中之一，`id`是菜单中存在项的唯一ID：

 * `before` - 在`id`对应的项的前面插入这个项。如果`id`对应的项不存在那么就插入到菜单的最后。
 * `after` - 在`id`对应的项的后面插入这个项。如果`id`对应的项不存在那么就插入到菜单的最后。
 * `endof` - 在包含`id`对应的项的逻辑组（使用分隔器创建组）的末尾插入这个项。如果`id`对应的项不存在，那么使用给定的`id`创建一个新的分隔，然后这个项将被插入到分隔器的后面。

当一个项被放置，在放置一个新的项之前，所有未放置的项被插入到这个已经放置的项的后面。所以如果你想要在同一个位置放置一组菜单项，你只需要指定这一组的第一个项的位置。

### 例子

模板：

    [
        {label: '4', id: '4'},
        {label: '5', id: '5'},
        {label: '1', id: '1', position: 'before=4'},
        {label: '2', id: '2'},
        {label: '3', id: '3'}
    ]

菜单：

    - 1
    - 2
    - 3
    - 4
    - 5

模板：

    [
        {label: 'a', position: 'endof=letters'},
        {label: '1', position: 'endof=numbers'},
        {label: 'b', position: 'endof=letters'},
        {label: '2', position: 'endof=numbers'},
        {label: 'c', position: 'endof=letters'},
        {label: '3', position: 'endof=numbers'}
    ]

菜单：

    - ---
    - a
    - b
    - c
    - ---
    - 1
    - 2
    - 3