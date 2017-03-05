# 2.autoUpdater

> 原文：https://github.com/electron/electron/blob/master/docs/api/auto-updater.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 使应用程序自己自动更新。   

进程：[主进程](https://github.com/electron/electron/blob/master/docs/glossary.md#main-process)   


`autoUpdater`模块提供一个[Squirrel](https://github.com/Squirrel)框架的接口。   

通过使用这些项目之一你可以快速启动一个多平台发布服务器来发布你的应用：   

 * [nuts](https://github.com/GitbookIO/nuts): *一个智能的发布你的应用的服务，使用GitHub作为后台。使用Squirrel自动更新（Mac和Windows）*   
 * [electron-release-server](https://github.com/ArekSredzki/electron-release-server): *一个全功能的自己托管的electron应用发布服务，兼容自动更新*   
 * [squirrel-updates-server](https://github.com/Aluxian/squirrel-updates-server): *一个基于使用GitHub的Squirrel.Mac和Squirrel.Windows发布的简单的node.js的服务*   
 * [squirrel-release-server](https://github.com/Arcath/squirrel-release-server): *一个基于Squirrel.Windows的简单的PHP应用，通过folder. Supports读取更新信息进行更新*

### 平台的注意事项   

虽然`autoUpdater`对不同平台提供一个统一的借口，但是每一个平台都有着细微的差别。   

##### MacOS   

MacOS中，`autoUpdater`模块是基于[Squirrel.Mac](https://github.com/Squirrel/Squirrel.Mac)的，这意味着你不需要任何特殊的设置就能让他工作。对于服务器端的要求，你可以阅读[Server Support](https://github.com/Squirrel/Squirrel.Mac#server-support)。请注意，[App Transport Security](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) (ATS) 适用于将所有的请求都作为更新过程的一部分。应用如果需要禁用ATS可以将`NSAllowsArbitraryLoads`添加到他们应用的plist文件中。   

**注意：**你的应用必须在MacOS上注册自动更新。这是`Squirrel.Mac`的要求。   

##### Windows   

Windows中，你需要在你使用`autoUpdater`之前安装你的应用到用户的机器上，所以建议你使用[electron-winstaller](https://github.com/electron/windows-installer)，[electron-builder](https://github.com/electron-userland/electron-builder)或者[grunt-electron-installer](https://github.com/electron/grunt-electron-installer)来打包生成一个Windows安装包。   

当使用[electron-winstaller]()或者[electron-builder]()时请确定你没有在第一次运行时尝试更新你的应用（可以[查看这个问题的更多信息](https://github.com/electron/electron/issues/7155)）。也同样建议使用[electron-squirrel-startup](https://github.com/mongodb-js/electron-squirrel-startup)来获取你应用程序的桌面快捷方式。   

使用Squirrel生成安装程序将会使用格式为`com.squirrel.PACKAGE_ID.YOUR_EXE_WITHOUT_DOT_EXE`的[Application User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx)创建一个快捷图标，例如`com.squirrel.slack.Slack`和`com.squirrel.code.Code`。你需要使用同样的ID调用你应用的`app.setAppUserModelId`接口，否则Windows将不允许你的应用程序放置在任务栏中。   

服务端的设置也是不同于MacOS。你可以阅读[Squirrel.Windows](https://github.com/Squirrel/Squirrel.Windows)的文档来获取更多信息。   

##### Linux   

Linux中没有内置支持自动更新，所以建议使用发布包管理器来更新你的应用。   

### 事件   

`autoUpdater`对象分发下面的事件：

##### 事件：'error'   

返回值：

 * `error` Error类型

更新出错时被分发。   

##### 事件：'checking-for-update'   

检查更新是否启动时被分发。

##### 事件：'update-available'   

当有可用的更新时被分发。这个更新将会被自动下载。

##### 事件：'update-not-available'   

当有没有可用的更新时被分发。

##### 事件：'update-downloaded'   

返回值：   

 * `event` Event类型
 * `releaseNotes` String类型
 * `releaseName` String类型
 * `releaseDate` Date类型
 * `updateURL` String类型

当一个更新下载完成时被分发。

Windows中只有`releaseName`是有效的。

### 方法   

`autoUpdater`对象有以下的方法：

##### autoUpdater.setFeedURL(url[, requestHeaders])   

 * `url` String类型
 * `requestHeaders` Object类型 *MacOS可用* （可选参数）- HTTP请求头。

设置`url`和初始化自动更新。    

##### autoUpdater.getFeedURL()   

返回值为`String`类型 - 当前的提供更新的URL。   

##### autoUpdater.checkForUpdates()   

询问服务器是否有更新。你必须在使用这个接口之前`setFeedURL`。

##### autoUpdater.quitAndInstall()

在下载完之后安装更新并且重启应用。这个接口需要在`update-downloaded`事件被分发之后调用。

**注意：**`autoUpdater.quitAndInstall()`将会首先关闭所有应用窗口，并且关闭之后只会分发`app`的`before-quit`事件。这里不同于一般的退出顺序。
