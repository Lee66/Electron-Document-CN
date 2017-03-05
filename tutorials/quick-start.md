# 1.快速开始

> 原文：https://github.com/electron/electron/blob/master/docs/tutorial/quick-start.md   
译者：[Lin](https://github.com/ShmilyLin)   


Electron提供包含了丰富的原生（操作系统）接口的运行时，使你能够使用纯粹的JavaScript创建桌面应用程序。你可以把它看作是一个变形的重点是在桌面应用程序而不是网页服务上的Node.js运行时。   

这并不意味着Electron是一个将JavaScript绑定到*图形用户界面（GUI）*上的库。相反，Electron使用网页作为它的GUI，所以你也可以把它看成是一个使用JavaScript控制的极小的Chromium浏览器。   

#### Main Process主进程   

Electron中，运行`package.json`’的`main`脚本的过程叫做**the main process(主进程)**。运行主进程的脚本会创建一个网页作为一个GUI来显示。   

#### Renderer Process渲染进程   

自从Electron使用Chromium来显示网页，Chromium的多进程架构也被使用在Electron中。Electron中的每一个网页都运行在它自己的进程中，这个就叫做**the renderer process(渲染进程)**。   

一般的浏览器中，网页通常在一个沙盒环境中运行，并且不允许使用原生资源。然而，Electron的使用者有能力在网页中使用Node.js的借口来与底层的操作系统进行交互。    

#### 主线程和渲染线程的不同   
主进程通过创建`BrowserWindow`实例来创建网页。每一个`BrowserWindow`实例运行网页在它自己的渲染进程中。当一个`BrowserWindow`实例被销毁的时候，对应的渲染进程也会被结束。
主进程管理所有的网页和他们对应的渲染进程。每一个渲染进程都是被隔离的，并且只会关心在它自己里面运行的网页。   

网页中，不允许调用本地GUI相关的接口，因为在网页中管理本地GUI资源是非常危险的并且也非常容易泄漏资源。如果你想要在网页中执行GUI操作，网页的渲染进程必须和主进程请求通讯，然后使用主进程之行这些操作。   

Electron中我们有几种在主进程和渲染进程之间通讯的方式。例如用来发送消息的[`ipcRenderer`](https://electron.atom.io/docs/api/ipc-renderer)和[`ipcMain`](https://electron.atom.io/docs/api/ipc-main)模块，以及RPC通讯方式的[remote](https://electron.atom.io/docs/api/remote)模块。这里还有一个FAQ条目来分享[如何在页面之间传输数据](https://electron.atom.io/docs/faq#how-to-share-data-between-web-pages)。   

### 编写你第一个Electron应用程序   

一般来说，一个Electron应用程序是按照这样的方式来构造的：   

    your-app/
    ├── package.json
    ├── main.js
    └── index.html

`package.json`的类型和Node模块的完全相同，指定脚本中`main`字段内是你的应用程序的启动脚本，它将会在主线程运行。一个你的`package.json`文件的例子看起来像这样：   

    {
        "name"    : "your-app",
        "version" : "0.1.0",
        "main"    : "main.js"
    }

**注意：**如果`package.json`文件中不存在`main`字段，Electron 将会默认加载一个index.js。   

`main.js`文件应该创建一个窗口并且处理系统事件，一个典型的例子是：   

    const {app, BrowserWindow} = require('electron')
    const path = require('path')
    const url = require('url')

    // 保持一个窗口对象的全局引用，如果你不这样做，当JavaScript对象被回收之后窗口将会被自动关闭
    let win

    function createWindow () {
        // 创建一个浏览器窗口
        win = new BrowserWindow({width: 800, height: 600})

        // 加载应用的index.html
        win.loadURL(url.format({
            pathname: path.join(__dirname, 'index.html'),
            protocol: 'file:',
            slashes: true
        }))

        // 打开DevTools.
        win.webContents.openDevTools()

        // 监听窗口关闭
        win.on('closed', () => {
            // 移除窗口对象的引用，
            // 如果你的应用支持多窗口你通常会将所有窗口存储在数组中，
            // 这个时候你应该删除相应的元素。
            win = null
        })
    }

    // 当Electron完成初始化并且准备创建一个浏览器窗口的时候会调用这个方法。
    // 一些接口只能在这个事件之后使用。
    app.on('ready', createWindow)

    // 当所有窗口都被关闭的时候退出。
    app.on('window-all-closed', () => {
        // macOS系统下对于应用这是很常见的，它们的菜单栏保持活跃直到他们使用Cmd + Q键确定退出
        if (process.platform !== 'darwin') {
            app.quit()
        }
    })

    app.on('activate', () => {
        // macOS系统下这是很常见的，当应用在dock上的图标被点击的时候（并且当时没有其他窗口是打开的），就会在应用中重新创建一个窗口。
        if (win === null) {
            createWindow()
        }
    })

    // 在这个文件中你可以包含其他你的应用程序在主线程中运行的代码部分。
    // 你也可以在把其他的代码放在其他的文件中，然后在这里require它们。
最后是你想展示的网页的index.html页面：   

    <!DOCTYPE html>
    <html>
        <head>
        <meta charset="UTF-8">
        <title>Hello World!</title>
        </head>
        <body>
            <h1>Hello World!</h1>
            We are using node <script>document.write(process.versions.node)</script>,
            Chrome <script>document.write(process.versions.chrome)</script>,
            and Electron <script>document.write(process.versions.electron)</script>.
        </body>
    </html>

### 运行你的应用程序   

一旦你已经创建了最开始`main.js`、`index.html`和`package.json`文件，你将可以尝试在本地运行测试你的应用程序，来确保它是可以正常的工作。   

##### **`electron`**   

`electron`是一个包含了Electron的预编译版本的`npm`模块。   
如果你安装了全局的`npm`，那么你只需要在你源代码的根目录下运行下面的命令就可以了：    

    electron .

如果你在本地安装了它，那么运行：   

*macOS / Linux*   

    $ ./node_modules/.bin/electron .

*Windows*   

    $ .\node_modules\.bin\electron .

##### 手动下载Electron二进制文件   

如果你手动下载Electron的二进制文件，你也可以使用已经包含的二进制文件直接之行你的应用程序：    

*Windows*   

    $ .\electron\electron.exe your-app\

*Linux*   

    $ ./electron/electron your-app/

*macOS*   

    $ ./Electron.app/Contents/MacOS/Electron your-app/

`Electron.app`是Electron发布的包的一部分，你可以在[这里](https://github.com/electron/electron/releases)下载它。      

##### 作为一个distribution运行   

当你完成你的应用程序的编写之后，你可以遵循[Application Distribution](https://electron.atom.io/docs/tutorial/application-distribution)指南创建一个distribution，然后执行打包应用程序。      

##### 试试这个例子   

在这个指导中通过使用[`electron/electron-quick-start`](https://github.com/electron/electron-quick-start)仓库来克隆并运行。   

**注意：**运行这个命令需要你的系统中有[Git](https://git-scm.com/)和[Node.js](https://nodejs.org/en/download/)（并且包含了[npm](https://npmjs.org/)）   

    # 克隆仓库
    $ git clone https://github.com/electron/electron-quick-start
    # 进入仓库
    $ cd electron-quick-start
    # 安装依赖
    $ npm install
    # 运行应用程序
    $ npm start

更多的应用程序例子，查看最好的Electron社区创建的[list of boilerplates](http://electron.atom.io/community/#boilerplates)。   