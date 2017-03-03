# 3.联网／脱机事件监听

> 原文：https://electron.atom.io/docs/tutorial/online-offline-events/   
译者：[Lin]()    

在线和离线事件监听可以在渲染进程中使用HTML接口执行，就像下面例子中展示的一样。   

*main.js*   

    const {app, BrowserWindow} = require('electron')

    let onlineStatusWindow

    app.on('ready', () => {
        onlineStatusWindow = new BrowserWindow({ width: 0, height: 0, show: false })
        onlineStatusWindow.loadURL(`file://${__dirname}/online-status.html`)
    })
*online-status.html*

    <!DOCTYPE html>
    <html>
    <body>
    <script>
        const alertOnlineStatus = () => {
            window.alert(navigator.onLine ? 'online' : 'offline')
        }

        window.addEventListener('online',  alertOnlineStatus)
        window.addEventListener('offline',  alertOnlineStatus)

        alertOnlineStatus()
    </script>
    </body>
    </html>

这里有可能有一些你想要在主进程中的同样事情上作出响应的实例。然而主进程没有`navigator`对象因此不能直接检测到这些事件。使用Electron的进程间通信工具，这些事件可以被转发到主进程并按照需要进行处理，就像下面的例子展示的那样。

*main.js*

    const {app, BrowserWindow, ipcMain} = require('electron')
    let onlineStatusWindow

    app.on('ready', () => {
        onlineStatusWindow = new BrowserWindow({ width: 0, height: 0, show: false })
        onlineStatusWindow.loadURL(`file://${__dirname}/online-status.html`)
    })

    ipcMain.on('online-status-changed', (event, status) => {
        console.log(status)
    })
*online-status.html*

    <!DOCTYPE html>
    <html>
    <body>
    <script>
        const {ipcRenderer} = require('electron')
        const updateOnlineStatus = () => {
            ipcRenderer.send('online-status-changed', navigator.onLine ? 'online' : 'offline')
        }

        window.addEventListener('online',  updateOnlineStatus)
        window.addEventListener('offline',  updateOnlineStatus)

        updateOnlineStatus()
    </script>
    </body>
    </html>

**注意**：如果Electron不能连接到局域网（LAN）或者路由器，它就被认为是离线的；其他的所有情况都会返回`true`。所以你可以认为是Electron离线时navigator.onLine返回一个`false`，你不能肯定的认为一个`true`值就代表Electron可以访问互联网。你可能会被误报，就像在计算机上运行的一个虚拟机总是“连接”上虚拟以太网适配器。因此，如果你想要确定网络状态，你需要其他的额外的检测手段。