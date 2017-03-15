# 1.app

> 原文：https://github.com/electron/electron/blob/master/docs/api/app.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 控制你应用程序的生命周期事件   

进程：[主进程](../../guides/glossary-of-terms.md#main-process)   

下面的例子将展示如何在最后一个窗口被关闭时关闭应用程序。   

    const {app} = require('electron')
    app.on('window-all-closed', () => {
        app.quit()
    })

## 事件

`app`对象会分发下面的事件。   

### 事件: ‘will-finish-launching’

应用程序完成基本的启动时分发。Windows和Linux下，`will-finish-launching`事件和`ready`事件是相同的；macOS下，这个事件代表了`NSApplication`中的`applicationWillFinishLaunching`通知。你通常会在这里设置`open-file`和`open-url`事件的监听，并且开始记录崩溃日志和自动更新。   

大多数情况下，你只需要在`ready`事件回调中做所有事情。   

### 事件: ‘ready’

返回值：   

 * `launchInfo`Object类型（macOS）   

Electron完成初始化时分发事件。MacOS中，`launchInfo`包含了`NSUserNotification`中用来打开应用程序的`userInfo`，如果它从通知中心被分发。如果这个事件被分发你可以调用`app.isReady()`检查。   

### 事件: ‘window-all-closed’

当所有窗口都被关闭时分发此事件。   

如果你没有订阅这个事件，那么当所有窗口都被关闭时默认行为是退出应用程序；然而，如果你订阅了，你可以控制是否退出应用程序。如果用户按`Cmd + Q`，或者开发者调用`app.quit()`，Electron将会首先尝试关闭所有窗口，然后分发`will-quit`事件，在这种情况下`window-all-closed`事件并不会被分发。   

### 事件: ‘before-quit’

返回值：   

 * `event` Event类型

应用程序开始关闭窗口之前分发事件。调用`event.preventDefault()`将阻止终止应用程序的默认行为。   

**注意：**如果应用程序是因为`autoUpdater.quitAndInstall()`而退出，那么`before-quit`将会在所有窗口分发`close`事件并且关闭所有窗口之后被分发。   

### 事件: ‘will-quit’

返回值：   

 * `event` Event类型   

所有窗口都已经关闭，应用程序将要退出的时候分发事件。调用event.preventDefault()将阻止终止应用程序的默认行为。   

查看`window-all-closed`事件和`will-quit and window-all-closed`事件描述上的区别。   

### 事件: ‘quit’

返回值：   

 * `event` Event类型   
 * `exitCode` Integer类型   

应用程序正在退出时被分发。   

### 事件: ‘open-file’ *MacOS*

返回值：    

 * `event` Event类型   
 * `path` String类型   

用户想要使用应用程序打开一个文件时被分发。`open-file`事件通常在应用程序已经打开并且系统想要应用程序重新文件时分发。`open-file`也在一个文件拖入到dock并且应用程序尚未运行时被分发。请确认非常早的监听`open-file`事件，以便在你应用程序启动时能够处理这个事件（甚至会在`ready`事件分发之前就会出现）。   

如果你想要处理这个事件，你需要调用`event.preventDefault()`。   

Windows中，你需要分析`process.argv`（在主进程中）来得到文件路径。   

### 事件: ‘open-url’ *MacOS*

返回值：   

 * `event` Event类型   
 * `url` String类型   

当用户想要使用应用程序打开一个地址时被分发。你的应用程序的`Info.plist`文件必须使用`CFBundleURLTypes`键定义url体系，并且在`AtomApplication`中设置`NSPrincipalClass`。   

如果你想要处理这个事件，你需要调用event.preventDefault()。   

### 事件: ‘activate’ *MacOS*

返回值：   

 * `event` Event类型    
 * `hasVisibleWindows` Boolean类型   

当用户点击应用程序在dock上的图标导致应用程序被激活时分发。   

### 事件: ‘continue-activity’ *MacOS*

返回值：   

 * `event` Event类型   
 * `type` String类型 - A string identifying the activity。对应`NSUserActivity.activityType`。   
 * `userInfo` Object类型 - Contains app-specific state stored by the activity on another device.   

Emitted during [Handoff](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html) when an activity from a different device wants to be resumed. 如果你想要处理这个事件，你需要调用event.preventDefault()。   

A user activity can be continued only in an app that has the same developer Team ID as the activity’s source app and that supports the activity’s type. Supported activity types are specified in the app’s Info.plist under the NSUserActivityTypes key.   

### 事件: ‘browser-window-blur’

返回值：   

 * `event` Event类型   
 * `window` BrowserWindow类型   

当一个[browserWindow](./BrowserWindow.md)失去焦点的时候被分发。   

### 事件: ‘browser-window-focus’   

返回值：   

 * `event` Event类型   
 * `window` BrowserWindow类型   

当一个[browserWindow](./BrowserWindow.md)获得焦点的时候被分发。     

### 事件: 'browser-window-created’   

返回值：   

 * `event` Event类型   
 * `window` BrowserWindow类型   

当一个新的[browserWindow](./BrowserWindow.md)被创建的时候被分发。    

### 事件: ‘certificate-error’

返回值：   

 * `event` Event类型   
 * `webContents` WebContents类型   
 * `url` String类型   
 * `error` String类型 - 错误代码   
 * `certificate` Certificate类型   
 * `callback` Function类型   
      * `isTrusted` Boolean类型 - 证书是否是可信的   

无法验证`url`的`certificate`时被分发，如果想要相信这个证书，并且你需要阻止默认行为你需要调用`event.preventDefault()`和`callback(true)`。   

    const {app} = require('electron')

    app.on('certificate-error', (event, webContents, url, error, certificate, callback) => {
        if (url === 'https://github.com') {
            // Verification logic.
            event.preventDefault()
            callback(true)
        } else {
            callback(false)
        }
    })

### 事件: ‘select-client-certificate’

返回值：   

 * `event` Event类型   
 * `webContents` WebContents类型   
 * `url` URL类型   
 * `certificateList` Certificate[]类型   
 * `callback` Function类型   
      * `certificate` Certificate类型（可选参数）   

请求客户端证书时被分发。   

`url`对应的是请求客户端证书的导航记录，`callback`被调用并且传入从列表中过滤的记录。使用`event.preventDefault()`阻止应用程序使用保存的第一个证书。   

    const {app} = require('electron')

    app.on('select-client-certificate', (event, webContents, url, list, callback) => {
        event.preventDefault()
        callback(list[0])
    })

### 事件: ‘login’

返回值：   

 * `event` Event类型   
 * `webContents` WebContents类型  
 * `request` Object类型   
      * `method` String类型   
      * `url` URL类型   
      * `referrer` URL类型   
 * `authInfo` Object类型   
      * `isProxy` Boolean类型   
      * `scheme` String类型   
      * `host` String类型   
      * `port` Integer类型   
      * `realm` String类型   
 * `callback` Function类型   
      * `username` String类型   
      * `password` String类型   

`webContents`想要做基本认证（auth）时被分发。   

默认行为是取消所有认证，想要重写你需要调用`event.preventDefault()`阻止默认行为，并且使用证书中的数据调用`callback(username, password)`。   

    const {app} = require('electron')

    app.on('login', (event, webContents, request, authInfo, callback) => {
        event.preventDefault()
        callback('username', 'secret')
    })

### 事件: ‘gpu-process-crashed’
返回值：   

 * `event` Event类型   
 * `killed` Boolean类型   

当GPU进程崩溃或者被杀死时被分发。   

### 事件: ‘accessibility-support-changed’ *MacOS,Windows*
返回值：   

 * `event` Event类型   
 * `accessibilitySupportEnabled` Boolean类型 - `true`是Chrome的accessibility支持被开启，`false`则是被停用。   

当Chrome的accessibility支持改变时被分发。这个事件在残疾人辅助功能，如屏幕阅读器被启用或停用时触发。详情见https://www.chromium.org/developers/design-documents/accessibility。   

## 方法

`app`对象有以下方法：   

**注意：**某些方法只适用于特定的操作系统，已经被标记。   

### `app.quit()`

尝试关闭所有窗口。`before-quit`事件将首先被分发。如果所有窗口都被成功的关闭则`will-quit`事件将会被分发，并且默认情况下应用程序将会被终止。   

这个方法保证了所有`beforeunload`和`unload`事件的回调被正确的执行。有可能在某个窗口取消退出并且在`beforeunload`事件中返回`false`。   

### `app.exit([exitCode])`

 * `exitCode` Integer类型（可选参数）   

传入参数`exitCode`立即退出。`exitCode`默认为0。   

除非告诉用户否则所有窗口立刻关闭，并且`before-quit`事件和`will-quit`事件将不会被分发。   

### `app.relaunch([options])`

 * `options` Object类型（可选参数）   
      * `args` String[]类型 -（可选参数）   
      * `execPath` String类型（可选参数）   

当前应用实例被退出之后重载应用。   

默认情况下，新的实例将会使用同样的工作目录和当前实例的命令行参数。当`args`参数被设置时，`args`参数将会被用来替代 命令行参数。当`execPath`参数被设置时，`execPath`参数将会被用来在重载替代当前应用。   

请注意，这个方法执行时不能退出应用，你需要在调用`app.relaunch`使应用重新运行之后再调用`app.quit`或者`app.exit`。
当`app.relaunch`被多次调用，多个实例将会在当前实例退出之后运行。   

一个立即重新启动当前实例并且添加新的命令行参数到新实例的例子：

    const {app} = require('electron')

    app.relaunch({args: process.argv.slice(1).concat(['--relaunch'])})
    app.exit(0)

### `app.isReady()`

返回值为`Boolean`类型 - 如果是`true`则表示Electron已经完成初始化，如果是`false`则表示尚未完成初始化。   

### `app.focus()`

Linux中，聚焦在第一个可见的窗口。MacOS中，使得应用程序变成激活状态的应用程序。Windows中，聚焦在应用程序的第一个窗口。   

### `app.hide()`

隐藏所有应用程序的窗口，而不是最小化它们。   

### `app.show()`

显示隐藏后的应用程序窗口。但不会自动聚焦它们。   

### `app.getAppPath()`

返回值为`String`类型 - 当前应用程序的目录。   

### `app.getPath(name)`

 * `name` String类型   

返回值为`String`类型 - 指定目录或文件的整体路径。如果发生故障将会抛出一个错误。   

你可以通过名字请求以下的路径：   

 * `home` 用户的主页目录   
 * `appData` 每个用户应用程序数据目录，默认情况下指向下面的路径：   
      * `%APPDATA%` 在Windows中   
      * `$XDG_CONFIG_HOME`或者`~/.config` 在Linux中   
      * `~/Library/Application Support` 在macOS中   
 * `userData 存储你的应用程序配置文件的目录，默认情况下是`appData`目录加上你的应用名称。   
 * `temp` 临时文件目录。   
 * `exe` 当前可执行文件。   
 * `module` `libchromiumcontent`库。   
 * `desktop` 当前用户桌面目录。   
 * `documents` 用户的“我的文档”的目录。   
 * `downloads` 用户的下载目录。   
 * `music` 用户的音乐目录。   
 * `pictures` 用户的图片目录。   
 * `videos` 用户的视频目录。   
 * `pepperFlashSystemPlugin` Pepper Flash插件在当前系统版本中的完整路径。   

### `app.setPath(name, path)`

 * `name` String类型   
 * `path` String类型   

使用指定的文件夹或者文件重写指定的`name`对应的路径。如果指定的文件夹不存在那么这个方法将自动创建这个文件夹。如果发生故障将会抛出一个错误。   

你只能重写`app.getPath`中定义的`name`对应的路径。   

默认情况下，网页的cookies和缓存将会被存储在`userData`目录下。如果你想要改变这个本地路径，你需要在`ready`事件被`app`模块分发之前重写`userData`对应的路径。   

### `app.getVersion()`

R返回值为`String`类型 - 已经加载的应用程序的版本号。如果在`package.json`文件内没有发现应用程序的版本号，那么就会返回当前包或者可执行文件的版本号。   

### `app.getName()`

返回值为`String`类型 - 写在应用程序`package.json`文件中的当前应用程序的名字。   

通常根据npm 模块说明，`package.json`中的`name`字段是短的小写名称。你通常都需要指定一个`productName`字段，这个字段是你应用程序的全部大写的名字，这个字段的名字Electron将会作为首选名称。   

### `app.setName(name)`

 * `name` String类型   

重写当前应用程序的名字。   

### `app.getLocale()`

返回值为`String`类型 - 当前应用程序的本地路径。可能的返回值都将会被记录在这里。   

**注意：**当发布你的打包好的应用时，你需要发送到本地文件夹。   

**注意：**Windows中你需要在`ready`事件被分发之后调用它。   

### `app.addRecentDocument(path)` *MacOS,Windows*

 * `path` String类型   

添加路径到最近使用的文档列表。   

这个列表由系统管理。Windows中你可以在任务栏查看这个列表，macOS中你可以在dock菜单中查看这个列表。   

### `app.clearRecentDocuments()` *MacOS,Windows*

清除最近使用的文件列表。   

### `app.setAsDefaultProtocolClient(protocol[, path, args])` *MacOS,Windows*

 * `protocol` String类型 - 你的协议的名字，不包含`://`。如果你想要你的应用处理`electron://`链接，则使用`electron`作为参数来调用这个方法。   
 * `path` String类型（可选参数） Windows可用 - 默认为`process.execPath`   
 * `args` String[]类型（可选参数）Windows可用 - 默认为空数组   

返回值为`Boolean`类型 - 是否调用成功。   

这个方法设置当前的可执行文件的默认处理协议（又名URI方案）。它将允许你集成的应用程序到系统的更深层次中。注册一次，所有的`your-protocol://`链接都将会使用当前的可执行文件打开。   

Windows中你可以提供可选的`path`参数，路径指向的可执行文件，当可执行文件启动时，`args`中的数组参数将会被传递到那个可执行文件中。   

**注意：**MacOS中你只能注册已经添加到你的应用程序的`info.plist`文件中的协议，是不可以在运行时间内更改的。然而你可以在编译的时候使用一个简单的文本编辑器或脚本来更改它。详情请参阅[Apple’s documentation](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-102207-TPXREF115)。   

这个接口使用Windows Registry和LSSetDefaultHandlerForURLScheme内核。   

### `app.removeAsDefaultProtocolClient(protocol[, path, args])` *MacOS,Windows*

 * `rotocol` String类型 - 你的协议的名字，不包含`://`。   
 * `path` String类型（可选参数）Windows可用 - 默认为`process.execPath`   
 * `args` String[]类型（可选参数）Windows可用  - 默认为空数组   

返回值为`Boolean`类型 - 是否调用成功。   

这个方法检查当前可执行文件是否会默认处理一个协议（又名URI方案）。如果会处理，将从应用中移除这个默认处理。   

### `app.isDefaultProtocolClient(protocol[, path, args])` *MacOS,Windows*

 * `protocol` String类型 - 你的协议的名字，不包含`://`。   
 * `path` String类型（可选参数）Windows可用 - 默认为`process.execPath`   
 * `args` String[]类型（可选参数）Windows可用 - 默认为空数组   

返回值为`Boolean`类型。   

这个方法检查当前可执行文件是否会默认处理一个协议（又名URI方案）。如果会处理，将会返回`true`，否则将会返回`false`。   

**注意：**MacOS中，如果这个应用程序已经注册了默认处理的协议，则你可以使用这个方法检查。你也可以通过macOS机器中的`~/Library/Preferences/com.apple.LaunchServices.plist`文件来检查。详情请参阅[Apple’s documentation](https://developer.apple.com/library/mac/documentation/Carbon/Reference/LaunchServicesReference/#//apple_ref/c/func/LSCopyDefaultHandlerForURLScheme)。   

这个接口使用Windows Registry和LSSetDefaultHandlerForURLScheme内核。   

### `app.setUserTasks(tasks)` *Windows*

 * `tasks` [Task[]](https://electron.atom.io/docs/api/structures/task)类型 - Task类型的对象的数组。   

Windows中添加`tasks`到跳转列表的[Tasks](http://msdn.microsoft.com/en-us/library/windows/desktop/dd378460(v=vs.85).aspx#tasks)部分。   

`tasks`是`Task`类型的对象的数组。   

返回值为`Boolean`类型 - 是否调用成功。   

**注意：**如果你想要自定义更多关于跳转列表的内容，请使用`app.setJumpList(categories)`。   

### `app.getJumpListSettings()` *Windows*

返回值为`Object`类型：   

 * `minItems` Integer类型 - 将在跳转列表中展示的项目的最小数量（关于这个值的更详细的描述请看[MSDN docs](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378398(v=vs.85).aspx)）。   
 * `removedItems` [JumpListItem[]](https://electron.atom.io/docs/api/structures/jump-list-item)类型 - `JumpListItem`对象类型的数组，`JumpListItem`对象对应的项是已经明确的从跳转列表中被用户删除的。这些项一定不能在下一次调用`app.setJumpList()`时被重新加入到跳转列表中，否则Windows将不会展示任何包含了删除项的内容。   

### `app.setJumpList(categories)` *Windows*

 * `categories` [JumpListCategory[]](https://electron.atom.io/docs/api/structures/jump-list-category)类型或者为`null` - `JumpListCategory`类型的对象的数组。   

设置或者删除一个应用程序中的自定义跳转列表，并且会返回一下字符串之一：   

 * `ok` - 没有出错。   
 * `error` - 发生了一个或者多个错误，运行日志能够可能造成的原因。   
 * `invalidSeparatorError` - 试图将一个分隔符加入到跳转列表的自定义部分。分隔符只允许在标准的`Tasks`部分中添加。   
 * `fileTypeRegistrationError` - 试图将一个应用程序没有注册可以处理的文件类型添加到跳转列表中。   
 * `customCategoryAccessDeniedError` - 由于设置用户隐私或者群组策略而不能添加到自定义跳转列表中。   

如果`categories`是`null`，之前设置的自定义跳转列表（如果有的话）将会被重新添加到应用程序的跳转列表中（Windows自动管理）。   

**注意：**如果一个`JumpListCategory`对象没有设置`type`属性和`name`属性，那么`type`属性将会被假设为`tasks`。如果`name`属性被设置而`type`属性没有被设置，那么`type`属性将被假设为`custom`。   

**注意：**用户可以从自定义的列表中移除项，Windows将不允许一个被移除的项重新添加回一个自定义列表，直到下次成功调用`app.setJumpList(categories).`之后。任何试图重新添加一个之前移除的项到一个自定义列表中的操作都将会被跳转列表忽略。获得列表中已经删除的项请使用`app.getJumpListSettings()`。   

这里有一个创建自定义列表的非常简单的例子：   

    const {app} = require('electron')

    app.setJumpList([
        {
            type: 'custom',
            name: 'Recent Projects',
            items: [
                { type: 'file', path: 'C:\\Projects\\project1.proj' },
                { type: 'file', path: 'C:\\Projects\\project2.proj' }
            ]
        },
        { // has a name so `type` is assumed to be "custom"
            name: 'Tools',
            items: [
                {
                    type: 'task',
                    title: 'Tool A',
                    program: process.execPath,
                    args: '--run-tool-a',
                    icon: process.execPath,
                    iconIndex: 0,
                    description: 'Runs Tool A'
                },
                {
                    type: 'task',
                    title: 'Tool B',
                    program: process.execPath,
                    args: '--run-tool-b',
                    icon: process.execPath,
                    iconIndex: 0,
                    description: 'Runs Tool B'
                }
            ]
        },
        { type: 'frequent' },
        { // has no name and no type so `type` is assumed to be "tasks"
            items: [
                {
                    type: 'task',
                    title: 'New Project',
                    program: process.execPath,
                    args: '--new-project',
                    description: 'Create a new project.'
                },
                { type: 'separator' },
                {
                    type: 'task',
                    title: 'Recover Project',
                    program: process.execPath,
                    args: '--recover-project',
                    description: 'Recover Project'
                }
            ]
        }
    ])

### `app.makeSingleInstance(callback)`

 * `callback` Function类型   
      * `argv` String[]类型 - 第二实例的命令行参数数组   
      * `workingDirectory` String类型 - 第二实例的工作目录   

这个方法使你的应用程序变成一个单例应用程序而不允许多个你的应用程序的实例运行，这个方法将会确保你的应用程序只有一个实例在运行，并且其他的实例会发送信息到这个实例上并且退出。   

当第二实例被运行的时候`callback`会被`callback(argv, workingDirectory)`调用。`argv`是第二实例的命令行参数的数组，`workingDirectory`是第二实例当前工作的目录。通常应用程序响应这个回掉来使得它们的第一窗口聚焦并且取消最小化。   

`callback`一定在`app`分发`ready`事件之后被执行。   

如果你操作的进程是应用程序的第一实例，则这个方法返回`false`并且你的应用会继续加载。如果你操作的进程已经发送参数给其他的实例，那么将会返回`true`，并且你需要立即退出这个实例。   

MacOS中，当用户尝试在Finder中打开一个你应用程序的第二实例时系统会自动的强制执行单例模式，并且`open-file`和`open-url`事件将会被分发。然而，当用户在命令行中启动你的应用程序时将会绕过系统的单例机制，你需要使用这个方法确保执行单粒模式。   

一个当第二实例启动时第一实例的窗口活动例子：   

    const {app} = require('electron')
    let myWindow = null

    const shouldQuit = app.makeSingleInstance((commandLine, workingDirectory) => {
        // Someone tried to run a second instance, we should focus our window.
        if (myWindow) {
            if (myWindow.isMinimized()) myWindow.restore()
                myWindow.focus()
        }
    })

    if (shouldQuit) {
        app.quit()
    }

    // Create myWindow, load the rest of the app, etc...
    app.on('ready', () => {
    })

### `app.releaseSingleInstance()`

释放所有通过`makeSingleInstance`创建的锁。这将允许应用程序的多个实例再一次并排运行。   

### `app.setUserActivity(type, userInfo[, webpageURL])` *MacOS*

 * `type` String类型 - 活动的唯一标识。对应[`NSUserActivity.activityType`](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUserActivity_Class/index.html#//apple_ref/occ/instp/NSUserActivity/activityType)。   
 * `userInfo` Object类型 - App-specific state to store for use by another device.
 * `webpageURL` String类型（可选参数）- The webpage to load in a browser if no suitable app is installed on the resuming device. The scheme must be http or https.   

创建一个`NSUserActivity`并且将它设置为当前的活动。这个活动是有资格在以后[Handoff](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html)到其他设备的。


### `app.getCurrentActivityType()` *MacOS*

返回值为`String`类型 - 当前正在运行的活动的类型。   

### `app.setAppUserModelId(id)` *Windows*

 * `id` String类型   

使用参数`id`更改[Application User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx)。   

### `app.importCertificate(options, callback)` *Linux*

 * `options` Object类型   
     * `certificate` String类型 - pkcs12文件的路径。
     * `password` String类型 - 证书的密码。
 * `callback` Function类型
     * `result` Integer类型 - 引用结果。

引用pkcs12类型的证书到到平台证书存储中。引用操作之后会将`result`传入调用`callback`，根据chromium的[net_error_list](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)，值为`0`时代表成功，其他任何值都代表失败。

### `app.disableHardwareAcceleration()`

禁用当前应用的硬件加速。   

这个方法只能在应用准备之前调用。   

### `app.setBadgeCount(count)` *MacOS,Linux*

 * `count` Integer类型   

返回值为`Boolean`类型 - 是否调用成功。   

给当前应用设置计数标识。设置为`0`将会隐藏这个标识。   

MacOS中它将展示在dock的图标上。Linux中它只在Unity的启动器上工作。   

**注意：**Unity启动器需要一个`.desktop`文件的实例才能工作，查看更多信息请阅读[Desktop Environment Integration](../../tutorials/desktop-environment-integration.md#unity-launcher-shortcuts-linux)。   

### `app.getBadgeCount()` *MacOS,Linux*

返回值是`Integer`类型 - 当前展示的计数标识的值。   

### `app.isUnityRunning()` *Linux*

返回值为Boolean - 当前的桌面面环境是否为Unity启动器。   

### `app.getLoginItemSettings([options])` *MacOS,Windows*

 * `options` Object类型（可选参数）   
     * `path` String类型（可选参数）Windows可用 - 与之前的可执行文件的目录相比较。默认是`process.execPath`。
     * `args` String类型 Windows可用 - 与之前的命令行参数相比较。默认是空数组。

如果你给`app.setLoginItemSettings`提供`path`和`args`选项 那么你需要通过同样的参数来正确的设置`openAtLogin`。   

返回值为`Object`类型:   

 * `openAtLogin` Boolean类型 - 如果应用在登录系统时打开则是`true`。   
 * `openAsHidden` Boolean类型 - 如果应用在登录系统时隐藏模式打开则是`true`。这个设置只有MacOS支持。   
 * `wasOpenedAtLogin` Boolean类型 - 如果应用在登录系统时已经自动打开则是`true`。这个设置只有MacOS支持。   
 * `wasOpenedAsHidden` Boolean类型 - 如果应用在作为一个隐藏启动项打开则是`true`。这表示应用在启动时并没有打开任何窗口。这个设置只有MacOS支持。   
 * `restoreState` Boolean类型 - 如果应用作为一个登录项已经被打开并且需要恢复之前的会话状态则是`true`。这表示应用需要恢复最近一次关闭应用时打开的窗口。这个设置只有MacOS支持。   

**注意：**这个接口在[MAS builds](../../guides/mac-app-store-submission-guide.md)上是没有效果的。

### `app.setLoginItemSettings(settings[, path, args])` *MacOS,Windows*

 * `settings` Object类型   
     * `openAtLogin` Boolean类型（可选参数）- `true`为设置应用在登录系统时打开，`false`则将应用从登录启动项中移除。默认是`false`。   
     * `openAsHidden` Boolean类型（可选参数）- `true`为设置应用隐藏打开。默认为`false`。用户可以从系统偏好设置中设置这个选项，所以当应用被打开时需要检查`app.getLoginItemStatus().wasOpenedAsHidden`来获取这个选项的当前值。这个设置仅有MacOS支持。   
     * `path` String类型（可选参数）Windows可用 - 登录时启动的可执行文件的路径。默认为`process.execPath`。   
     * `args` String类型 *Windows可用* - 传递给可执行文件的命令行参数。默认为空的数组。注意使用引号包裹路径。

设置应用的登录选项。   

Windows中和Electron的`autoUpdater`一起工作，which uses [Squirrel](https://github.com/Squirrel/Squirrel.Windows)，你将想要设置Update.exe的启动路径，并且通过参数指定你的应用名称。例如：   

    const appFolder = path.dirname(process.execPath)
    const updateExe = path.resolve(appFolder, '..', 'Update.exe')
    const exeName = path.basename(process.execPath)

    app.setLoginItemSettings({
        openAtLogin: true,
        path: updateExe,
        args: [
            '--processStart', `"${exeName}"`,
            '--process-start-args', `"--hidden"`
        ]
    })

**注意：**这个接口在[MAS builds](../../guides/mac-app-store-submission-guide.md)上是没有效果的。   

### `app.isAccessibilitySupportEnabled()` *MacOS,Windows*

返回值为`Boolean`类型 - 如果Chrome的辅助功能被开启则是`true`，`false`则是尚未开启。如果检测到使用了辅助技术，比如屏幕阅读，这个接口将会返回`true`。详细请见https://www.chromium.org/developers/design-documents/accessibility。   

### `app.setAboutPanelOptions(options)` *MacOS*

 * `options` Object类型   
     * `applicationName` String类型（可选参数）- 应用的名字。   
     * `applicationVersion` String类型（可选参数）- 应用的版本。   
     * `copyright` String类型（可选参数）- Copyright信息。   
     * `credits` String类型（可选参数）- 信用信息。   
     * `version` String类型（可选参数）- 应用的开发版本号。   

设置关于面板的选项。这将会覆盖应用的`.plist`文件中的定义的值。查看[Apple docs](https://developer.apple.com/reference/appkit/nsapplication/1428479-orderfrontstandardaboutpanelwith?language=objc)获取更多信息。   

### `app.commandLine.appendSwitch(switch[, value])`

 * `switch` String类型 - 一个命令行开关
 * `value` String类型（可选参数）- 传给开关的值

给Chromium命令行添加一个开关（使用`value`参数）。

**注意：**这将不会影响`process.argv`，通常使用这个方法控制一些底层的Chromium行为。

### `app.commandLine.appendArgument(value)`

 * `value` String类型 - 这个参数将会被加入到命令行中。

添加一个参数到Chromium的命令行。这个参数将会被正确的引用。

**注意：**这将不会影响`process.argv`。

### `app.dock.bounce([type])` *MacOS*

 * `type` String类型（可选参数）- 可以是`critical`或者`informational`。默认是`informational`。   

设置为`critical`时，dock的图标将会不断跳动，直到这个应用被激活或者请求被取消。   

设置为`informational`时，dock的图标将跳动一秒。然而这个请求一直有效，直到这个应用被激活或者请求被取消。   

返回值为代表着请求的`Integer`类型的ID。   

### `app.dock.cancelBounce(id)` *MacOS*

 * `id` Integer类型   

通过`id`来取消跳动。

### `app.dock.downloadFinished(filePath)` *MacOS*

 * `filePath` String类型   

如果`filePath`指向的是Downloads文件夹则会使下载图标跳动。   

### `app.dock.setBadge(text)` *MacOS*

 * `text` String类型   

设置在dock区域显示的字符串。   

### `app.dock.getBadge()` *MacOS*

返回值为`String`类型 - dock区域显示的字符串。   

### `app.dock.hide()` *MacOS*

隐藏dock上的图标。   

### `app.dock.show()` *MacOS*

展示dock上的图标。   

### `app.dock.isVisible()` *MacOS*

返回值为`Boolean`类型 - dock上的图标是否可见。`app.dock.show()`是异步调用，所以可能不会在调用后立刻返回结果。

### `app.dock.setMenu(menu)` *MacOS*

 * `menu` Menu类型

设置应用的[dock菜单](https://developer.apple.com/library/mac/documentation/Carbon/Conceptual/customizing_docktile/concepts/dockconcepts.html#//apple_ref/doc/uid/TP30000986-CH2-TPXREF103)。

### `app.dock.setIcon(image)` *MacOS*

 * `image` (NativeImage | String)   

设置应用在dock中的图标。   