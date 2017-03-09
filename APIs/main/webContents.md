# 17.webContents

> 原文：https://github.com/electron/electron/blob/master/docs/api/web-contents.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 渲染和控制网页

进程：[主进程](../../guides/glossary-of-terms.html#main-process)   

`webContents`是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)。它负责渲染和控制一个网页，并且是[`BrowserWindow`](./BrowerWindow.html)对象的一个属性。一个访问`webContents`对象的例子：

    const {BrowserWindow} = require('electron')

    let win = new BrowserWindow({width: 800, height: 1500})
    win.loadURL('http://github.com')

    let contents = win.webContents
    console.log(contents)

<h2 id="Methods">方法</h2>

这里的方法可以通过`webContents`模块来访问：

    const {webContents} = require('electron')
    console.log(webContents)

<h3 id="webContents-getAllWebContents"><code>webContents.getAllWebContents()</code></h3>

返回值为`WebContents[]`类型 - 所有`WebContents`实例组成的数组。这个将包含所有窗口、webview、打开的工具、和工具扩展北京页面的网络内容。

<h3 id="webContents-getFocusedWebContents"><code>webContents.getFocusedWebContents()</code></h3>

返回值为`WebContents`类型 - 应用中的获得焦点的网络内容，如果没有就返回`null`。

<h3 id="webContents-fromId"><code>webContents.fromId(id)</code></h3>

 * `id` Integer类型

返回值为`WebContents`类型 - 通过给的ID获取一个对应的`WebContents`实例。

<h2 id="class-webcontents">Class: WebContents</h2>

> 渲染和控制一个BrowserWindow实例的内容。

进程：[主进程](../../guides/glossary-of-terms.html#main-process)

<h3 id="instance-events">实例事件</h3>

<h4 id="event-did-finish-load">事件：'did-finish-load'</h4>

当导航被加载完成时被分发，即标签上的旋转标志已经停止旋转，并且`onload`事件被分发。

<h4 id="event-did-fail-load">事件：'did-fail-load'</h4>

返回值为：

 * `event` Event类型
 * `errorCode` Integer类型
 * `errorDescription` String类型
 * `validatedURL` String类型
 * `isMainFrame` Boolean类型

这个事件和`did-finish-load`类似，但是是在当加载失败或者取消加载的时候被分发，例如`window.stop()`被调用。完整的错误代码列表和它们对应的意思在[这里](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)。

<h4 id="event-did-frame-finish-load">事件：'did-frame-finish-load'</h4>

返回值为：

 * `event` Event类型
 * `isMainFrame` Boolean类型

当一个frame完成导航的时候被分发。

<h4 id="event-did-start-loading">事件：'did-start-loading'</h4>

当标签上的旋转标志开始旋转时的时间点上。

<h4 id="event-did-stop-loading">事件：'did-stop-loading'</h4>

当标签上的旋转标志停止旋转时的时间点上。

<h4 id="event-did-get-response-details">事件：'did-get-response-details'</h4>

返回值为：

 * `event` Event类型
 * `status` Boolean类型
 * `newURL` String类型
 * `originalURL` String类型
 * `httpResponseCode` Integer类型
 * `requestMethod` String类型
 * `referrer` String类型
 * `headers` Object类型
 * `resourceType` String类型

当关于一个请求资源的详细信息有效时被分发。`status`标识着socket连接到了下载的资源上。

<h4 id="event-did-get-redirect-request">事件：'did-get-redirect-request'</h4>

返回值为：

 * `event` Event类型
 * `oldURL` String类型
 * `newURL` String类型
 * `isMainFrame` Boolean类型
 * `httpResponseCode` Integer类型
 * `requestMethod` String类型
 * `referrer` String类型
 * `headers` Object类型

当一个请求资源收到一个重定向时被分发。

<h4 id="event-dom-ready">事件：'dom-ready'</h4>

返回值为：

 * `event` Event类型

当给定的frame中的文档被加载的时候被分发。

<h4 id="event-page-favicon-updated">事件：'page-favicon-updated'</h4>

返回值为：

 * `event` Event类型
 * `favicons` String[]类型 - URL的数组

当页面收到网站图标地址时被分发。

<h4 id="event-new-window">事件：'new-window'</h4>

返回值为：

 * `event` Event类型
 * `url` String类型
 * `frameName` String类型
 * `disposition` String类型 - 可以是`default`、`foreground-tab`、`background-tab`、`new-window`、`save-to-disk`和`other`。
 * `options` Object类型 - 这个选项将会被用来创建一个新的`BrowserWindow`。
 * `additionalFeatures` String[]类型 - 这个不标准的特性（这个特性不会被Electron或Chromium处理）传给`window.open()`。

当页面请求通过一个`url`打开一个新的窗口时被分发。它可以被`window.open`或一个像`<a target='_blank'>`似的外部链接所请求。

默认情况下会通过这个`url`创建一个新的`BrowserWindow`。

调用`event.preventDefault()`将会阻止Electron自动的创建一个新的`BrowserWindow`。如果你想调用`event.preventDefault()`并且手动创建一个新的`BrowserWindow`那么你必须设置`event.newGuest`引用这个新的`BrowserWindow`实例，如果不这样做可能会导致想不到的行为。例如：

    myBrowserWindow.webContents.on('new-window', (event, url) => {
        event.preventDefault()
        const win = new BrowserWindow({show: false})
        win.once('ready-to-show', () => win.show())
        win.loadURL(url)
        event.newGuest = win
    })

<h4 id="event-will-navigate">事件：'will-navigate'</h4>

返回值为：

 * `event` Event类型
 * `url` String类型

当一个用户或者页面想要开始导航时被分发。它可以发生在`window.location`对象被改变或者用户点击页面中的了一个链接时。

当导航以代码的形式调用`webContents.loadURL`和`webContents.back`之类的接口而开始时这个事件将不会被分发。

页面之内的导航也不会使这个事件被分发，例如点击锚点链接或者更新`window.location.hash`。对于这些行为请使用`did-navigate-in-page`事件。

调用`event.preventDefault()`将会阻止导航。

<h4 id="event-did-navigate">事件：'did-navigate'</h4>

返回值为：

 * `event` Event类型
 * `url` String类型

当一个导航完成时被分发。

页面之内的导航也不会使这个事件被分发，例如点击锚点链接或者更新`window.location.hash`。对于这些行为请使用`did-navigate-in-page`事件。

<h4 id="event-did-navigate-in-page">事件：'did-navigate-in-page'</h4>

返回值为：

 * `event` Event类型
 * `url` String类型
 * `isMainFrame` Boolean类型

当一个页面内的导航发生的时候被分发。

当一个页面内的导航发生，这个页面的URL被改变，但是不会导致导航到这个页面之外。例如这个事件是当锚点链接被点击或者当DOM的`hashchange`事件被触发时。

<h4 id="event-crashed">事件：'crashed'</h4>

返回值为：

 * `event` Event类型
 * `killed` Boolean类型

当渲染进程崩溃或者被杀死的时候被分发。

<h4 id="event-plugin-crashed">事件：'plugin-crashed'</h4>

返回值为：

 * `event` Event类型
 * `name` String类型
 * `version` String类型

当一个插件进程崩溃时被分发。

<h4 id="event-destroyed">事件：'destroyed'</h4>

当`webContents`被销毁的时候被分发。

<h4 id="event-before-input-event">事件：'before-input-event'</h4>

返回值为：

 * `event` Event
 * `input` Object - 输入属性
     * `type` String - `keyUp`或者`keyDown`
     * `key` String - 相当于[KeyboardEvent.key](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)
     * `code` String - 相当于[KeyboardEvent.code](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)
     * `isAutoRepeat` Boolean - 相当于[KeyboardEvent.repeat](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)
     * `shift` Boolean - 相当于[KeyboardEvent.shiftKey](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)
     * `control` Boolean - 相当于[KeyboardEvent.controlKey](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)
     * `alt` Boolean - 相当于[KeyboardEvent.altKey](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)
     * `meta` Boolean - 相当于[KeyboardEvent.metaKey](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent)

在页面中的`keydown`和`keyup`事件被发出之前被分发。调用`event.preventDefault`将阻止页面的`keydown/keyup`事件发出。

<h4 id="event-devtools-opened">事件：'devtools-opened'</h4>

当开发工具被打开的时候被分发。

<h4 id="event-devtools-closed">事件：'devtools-closed'</h4>

当开发工具被关闭的时候被分发。

<h4 id="event-devtools-focused">事件：'devtools-focused'</h4>

当开发工具被聚焦／打开的时候被分发。

<h4 id="event-certificate-error">事件：'certificate-error'</h4>

返回值为：

 * `event` Event类型
 * `url` String类型
 * `error` String类型 - 错误代码
 * `certificate` Certificate类型
 * `callback` Function类型
     * `isTrusted` Boolean类型 - 表示经过思考这个证书是否是可信的。

当验证`url`指向的证书失败时被分发。

使用起来和[`app`的`certificate-error`事件](./app.html#event-certificate-error)一样。

<h4 id="event-select-client-certificate">事件：'select-client-certificate'</h4>

返回值为：

 * `event` Event类型
 * `url` URL类型
 * `certificateList` [Certificate[]](https://github.com/electron/electron/blob/master/docs/api/structures/certificate.md)类型
 * `callback` Function类型
     * `certificate` [Certificate](https://github.com/electron/electron/blob/master/docs/api/structures/certificate.md)类型 - 必须从给定的列表中选择一个证书

当请求一个客户端证书时被分发。

使用起来和[`app`的`select-client-certificate`事件](./app.html#event-select-client-certificate)一样。

<h4 id="event-login">事件：'login'</h4>

返回值为：

 * `event` Event
 * `request` Object
     * `method` String
     * `url` URL
     * `referrer` URL
 * `authInfo` Object
     * `isProxy` Boolean
     * `scheme` String
     * `host` String
     * `port` Integer
     * `realm` String
 * `callback` Function
     * `username` String
     * `password` String

webContents想要进行基本验证的时候被分发。

使用起来和[`app`的`login`事件](./app.html#event-login)一样。

<h4 id="event-found-in-page">事件：'found-in-page'</h4>

返回值为：

 * `event` Event类型
 * `result` Object类型
     * `requestId` Integer类型
     * `activeMatchOrdinal` Integer类型 - 主动匹配的位置。
     * `matches` Integer类型 - 匹配的数量。
     * `selectionArea` Object类型 - 第一匹配区域的坐标。

[`webContents.findInPage`]请求有有效的结果时被分发。

<h4 id="event-media-started-playing">事件：'media-started-playing'</h4>

媒体开始播放时被分发。

<h4 id="event-media-paused">事件：'media-paused'</h4>

媒体被暂停或者完成播放时被分发。

<h4 id="event-did-change-theme-color">事件：'did-change-theme-color'</h4>

页面的主题颜色改变时被分发。这个通常由于有一个meta标签：

    <meta name='theme-color' content='#ff0000'>

<h4 id="event-update-target-url">事件：'update-target-url'</h4>

返回值为：

 * `event` Event类型
 * `url` String类型

当鼠标移动到一个链接或者键盘移动光标到链接上时被分发。

<h4 id="event-cursor-changed">事件：'cursor-changed'</h4>

返回值为：

 * `event` Event类型
 * `type` String类型
 * `image` NativeImage类型（可选参数）
 * `scale` Float类型（可选参数）- 自定义光标的缩放因数
 * `size` Object类型（可选参数）- 图片的尺寸
     * `width` Integer类型
     * `height` Integer类型
 * `hotspot` Object类型（可选参数）- 自定义光标的热点的坐标coordinates of the custom cursor's hotspot
     * `x` Integer类型 - x坐标
     * `y` Integer类型 - y坐标

当光标的类型改变的时候被分发。`type`参数可以是`default`、`crosshair`、`pointer`、`text`、`wait`、`help`、`e-resize`、`n-resize`, `ne-resize`、`nw-resize`、`s-resize`、`se-resize`、`sw-resize`、`w-resize`、`ns-resize`、`ew-resize`、`nesw-resize`、`nwse-resize`、`col-resize`、`row-resize`、`m-panning`、`e-panning`、`n-panning`、`ne-panning`、`nw-panning`、`s-panning`、`se-panning`、`sw-panning`、`w-panning`、`move`、`vertical-text`、`cell`、`context-menu`、`alias`、`progress`、`nodrop`、`copy`、`none`、`not-allowed`、`zoom-in`、`zoom-out`、`grab`、`grabbing`、`custom`。

如果`type`参数是`custom`，`image`参数将是一个`NativeImage`类型的自定义光标图标，`scale`，`size`和`hotspot`也是关于自定义光标的附加信息。

<h4 id="event-context-menu">事件：'context-menu'</h4>

返回值为：

 * `event` Event类型
 * `params` Object类型
     * `x` Integer类型 - x坐标
     * `y` Integer类型 - y坐标
     * `linkURL` String类型 - 调用了右键菜单的节点外层包裹着的链接的URL。
     * `linkText` String类型 - 与链接相关的文本。如果连接的内容是一张图片那么这个字段可能是一个空的字符串。
     * `pageURL` String类型 - 调用了右键菜单的顶级页面的URL。
     * `frameURL` String类型 - 调用了右键菜单的subframe的URL。
     * `srcURL` String类型 - 调用了右键菜单的元素的源地址。元素的源地址是图片，音频和视频。
     * `mediaType` String类型 - 调用了右键菜单的节点的类型。可以是`none`、`image`、`audio`、`video`、`canvas`、`file`或`plugin`。
     * `hasImageContents` Boolean类型 - 右键菜单是否被一个不为空的图片所调用。
     * `isEditable` Boolean类型 - 上下文是否可以被编辑。
     * `selectionText` String类型 - 调用了右键菜单的被选中的文字。
     * `titleText` String类型 - 调用了右键菜单的标题或者alt text。
     * `misspelledWord` String类型 - 在鼠标下方的拼写错误的单词，如果有的话。
     * `frameCharset` String类型 - 调用了右键菜单的地方的frame使用的字符编码格式。
     * `inputFieldType` String类型 - 如果在输入区调用了右键菜单，这个字段是输入区的类型。可能的值有`none`、`plainText`、`password`、`other`。
     * `menuSourceType` String类型 - 调用了右键菜单的输入源。可能是`none`、`mouse`、`keyboard`、`touch`、`touchMenu`。
     * `mediaFlags` Object类型 - 调用了右键菜单的媒体元素的标识。
         * `inError` Boolean类型 - 媒体元素是否已经崩溃。
         * `isPaused` Boolean类型 - 媒体元素是否被暂停。
         * `isMuted` Boolean类型 - 媒体元素是否被静音。
         * `hasAudio` Boolean类型 - 媒体元素是否有音频。
         * `isLooping` Boolean类型 - 媒体元素是否是循环播放。
         * `isControlsVisible` Boolean类型 - 媒体元素的控制器是否是可见的。
         * `canToggleControls` Boolean类型 - 媒体元素的控制器是否是可以操作的。
         * `canRotate` Boolean类型 - 媒体元素是否可以被旋转。
     * `editFlags` Object类型 - 这个标识表示渲染器认为它是否能够执行对应的操作。
         * `canUndo` Boolean类型 - 渲染器认为它是否可以撤销操作。
         * `canRedo` Boolean类型 - 渲染器认为它是否可以重做操作。
         * `canCut` Boolean类型 - 渲染器认为它是否可以剪切。
         * `canCopy` Boolean类型 - 渲染器认为它是否可以复制。
         * `canPaste` Boolean类型 - 渲染器认为它是否可以粘贴。
         * `canDelete` Boolean类型 - 渲染器认为它是否可以删除。
         * `canSelectAll` Boolean类型 - 渲染器认为它是否可以选择全部。

当这里有一个新的需要处理的右键菜单时会被分发。

<h4 id="event-select-bluetooth-device">事件：'select-bluetooth-device'</h4>

返回值为：

 * `event` Event类型
 * `devices` [BluetoothDevice[]](https://github.com/electron/electron/blob/master/docs/api/structures/bluetooth-device.md)类型
 * `callback` Function类型
     * `deviceId` String类型

当调用`navigator.bluetooth.requestDevice`而需要选择蓝牙设备的时候被分发。`webBluetooth`应该被允许使用`navigator.bluetooth`接口。如果`event.preventDefault`没有被调用，将会默认选择第一个活动的设备。`callback`需要使用被选择的`deviceId`来调用，传入空字符串给`callback`将取消这个请求。

    const {app, webContents} = require('electron')
    app.commandLine.appendSwitch('enable-web-bluetooth')

    app.on('ready', () => {
        webContents.on('select-bluetooth-device', (event, deviceList, callback) => {
            event.preventDefault()
            let result = deviceList.find((device) => {
                return device.deviceName === 'test'
            })
            if (!result) {
                callback('')
            } else {
                callback(result.deviceId)
            }
        })
    })

<h4 id="event-paint">事件：'paint'</h4>

返回值为：

 * `event` Event类型
 * `dirtyRect` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型
 * `image` [NativeImage](https://github.com/electron/electron/blob/master/docs/api/native-image.md)类型 - 整个frame的图片数据。

当一个新的frame被生成的时候会被分发。只有脏区被传入缓冲区。

    const {BrowserWindow} = require('electron')

    let win = new BrowserWindow({webPreferences: {offscreen: true}})
    win.webContents.on('paint', (event, dirty, image) => {
        // updateBitmap(dirty, image.getBitmap())
    })
    win.loadURL('http://github.com')

<h4 id="event-devtools-reload-page">事件：'devtools-reload-page'</h4>

当开发工具窗口通知`webContents`重载时被分发。

<h4 id="event-will-attach-webview">事件：'will-attach-webview'</h4>

返回值为：

 * `event` Event类型
 * `webPreferences` Object类型 - 网络参数将被宾客页面使用。可以修改这个对象来适应宾客页面的参数。
 * `params` Object类型 - 其他的`<webview>`参数，比如说`src`地址。可以修改这个对象来适应宾客页面的参数。

当一个`<webview>`的网络内容被关联到这个网络内容上的时候被分发。调用`event.preventDefault()`将销毁宾客页面。

这个事件可以被用来配置一个已经被加载的`<webview>`的`webContents`中的`webPreferences`，提供设置一些配置选项的能力，而这些配置选项不能通过`<webview>`的参数来设置。

<h3 id="instance-methods">实例方法</h3>

<h4 id="contents-loadURL"><code>contents.loadURL(url[, options])</code></h4>

 * `url` String类型
 * `options` Object类型（可选参数）
     * `httpReferrer` String类型（可选参数）- 一个HTTP链接的地址。
     * `userAgent` String类型（可选参数）- 一个用户代理发出的请求。
     * `extraHeaders` String类型（可选参数）- 由“\n”分隔的附加的头部。
     * `postData` (UploadRawData | UploadFile | UploadFileSystem | UploadBlob)[]类型 -（可选参数）
     * `baseURLForDataURL` String类型（可选参数）- 通过数据地址被加载的文件的基本地址（路径分隔符后面的部分）。如果指定的`url`是一个数据地址并且需要加载其他的文件时，才需要这个参数。

加载窗口中的`url`。这个`url`必须包含了协议前缀，比如说`http://`或者`file://`。如果加载需要绕过http缓存，那么需要使用`pragma`头来实现它。

    const {webContents} = require('electron')
    const options = {extraHeaders: 'pragma: no-cache\n'}
    webContents.loadURL('https://github.com', options)

<h4 id="contents-downloadURL"><code>contents.downloadURL(url)</code></h4>

 * `url` String类型

不导航过去就开始下载`url`中的资源。`session`的`will-download`事件将会被分发。

<h4 id="contents-getURL"><code>contents.getURL()</code></h4>

返回值为`String`类型 - 当前页面的地址。

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow({width: 800, height: 600})
    win.loadURL('http://github.com')

    et currentURL = win.webContents.getURL()
    console.log(currentURL)

<h4 id="contents-getTitle"><code>contents.getTitle()</code></h4>

返回值为`String`类型 - 当前页面的标题。

<h4 id="contents-isDestroyed"><code>contents.isDestroyed()</code></h4>

返回值为`Boolean`类型 - 当前页面是否被销毁。

<h4 id="contents-isFocused"><code>contents.isFocused()</code></h4>

返回值为`Boolean`类型 - 当前页面是否聚焦。

<h4 id="contents-isLoading"><code>contents.isLoading()</code></h4>

返回值为`Boolean`类型 - 当前页面是否仍在加载资源。

<h4 id="contents-isLoadingMainFrame"><code>contents.isLoadingMainFrame()</code></h4>

返回值为`Boolean`类型 - 主frame（不仅仅它内部的是iframes或frames）是否仍在加载。

<h4 id="contents-isWaitingForResponse"><code>contents.isWaitingForResponse()</code></h4>

返回值为`Boolean`类型 - 网页是否是等待页面主要资源的一个第一响应。

<h4 id="contents-stop"><code>contents.stop()</code></h4>

停止任何尚未完成的导航。

<h4 id="contents-reload"><code>contents.reload()</code></h4>

重载当前的网页。

<h4 id="contents-reloadIgnoringCache"><code>contents.reloadIgnoringCache()</code></h4>

忽视缓存并重载当前页面。

<h4 id="contents-canGoBack"><code>contents.canGoBack()</code></h4>

返回值为`Boolean`类型 - 浏览器是否可以返回之前的网页。

<h4 id="contents-canGoForward"><code>contents.canGoForward()</code></h4>

返回值为`Boolean`类型 - 浏览器是否可以转去下一个页面。

<h4 id="contents-canGoToOffset"><code>contents.canGoToOffset(offset)</code></h4>

 * `offset` Integer类型

返回值为`Boolean`类型 - 网页是否可以定位到`offset`。

<h4 id="contents-clearHistory"><code>contents.clearHistory()</code></h4>

清空导航历史记录。

<h4 id="contents-goBack"><code>contents.goBack()</code></h4>

让浏览器返回一个页面。

<h4 id="contents-goForward"><code>contents.goForward()</code></h4>

让浏览器前进一个页面。

<h4 id="contents-goToIndex"><code>contents.goToIndex(index)</code></h4>

 * `index` Integer类型

通过指定的网页索引，导航浏览器到对应的页面。

<h4 id="contents-goToOffset"><code>contents.goToOffset(offset)</code></h4>

 * `offset` Integer类型

从“当前的位置”导航到指定的偏移位置。

<h4 id="contents-isCrashed"><code>contents.isCrashed()</code></h4>

返回值为`Boolean`类型 - 渲染线程是否已经崩溃。

<h4 id="contents-setUserAgent"><code>contents.setUserAgent(userAgent)</code></h4>

 * `userAgent` String类型

对这个网页重写用户代理。

<h4 id="contents-getUserAgent"><code>contents.getUserAgent()</code></h4>

返回值为`String`类型 - 这个网页的用户代理。

<h4 id="contents-insertCSS"><code>contents.insertCSS(css)</code></h4>

 * `css` String类型

给当前网页注入一个CSS样式。

<h4 id="contents-executeJavaScript"><code>contents.executeJavaScript(code[, userGesture, callback])</code></h4>

 * `code` String类型
 * `userGesture` Boolean类型（可选参数）- 默认是`false`。
 * `callback` Function类型（可选参数）- 脚本执行之后被调用。
     * `result` Any类型

返回值为`Promise`类型 - promise可能是带有执行代码的结果的resolves，或者如果代码执行失败那么就是一个rejected promise。

对页面中`code`的评估。

在浏览器窗口中，一些HTML接口，类似于`requestFullScreen`，可以只被用户的一个手势而激发。设置`userGesture`为`true`将会移除这个限制。

如果执行代码的结果是一个promise，那么回调结果将是这个promise的resolved的值。我们建议你使用返回的Promise来处理Promise中results的代码。

    contents.executeJavaScript('fetch("https://jsonplaceholder.typicode.com/users/1").then(resp => resp.json())', true)
        .then((result) => {
            console.log(result) // Will be the JSON object from the fetch call
        })

<h4 id="contents-setAudioMuted"><code>contents.setAudioMuted(muted)</code></h4>

 * `muted` Boolean类型

静音当前网页中的音频。

<h4 id="contents-isAudioMuted"><code>contents.isAudioMuted()</code></h4>

返回值为`Boolean`类型 - 这个页面是否已经被静音。

<h4 id="contents-setZoomFactor"><code>contents.setZoomFactor(factor)</code></h4>

 * `factor` Number类型 - 缩放因数。

改变缩放因数为设置的缩放因数。缩放因数是缩放百分比除以100，所以300% = 3.0。

<h4 id="contents-getZoomFactor"><code>contents.getZoomFactor(callback)</code></h4>

 * `callback` Function类型
     * `zoomFactor` Number类型

发送一个请求来获得当前的缩放因数，`callback`将会被`callback(zoomFactor)`调用。

<h4 id="contents-setZoomLevel"><code>contents.setZoomLevel(level)</code></h4>

 * `level` Number类型 - 缩放等级

改变缩放等级为设置的等级。原始大小为0，每一个增加或减小都将放大或缩小20%，默认的最大是300%，最小是50%。

<h4 id="contents-getZoomLevel"><code>contents.getZoomLevel(callback)</code></h4>

 * `callback` Function类型
     * `zoomLevel` Numberl类型

发送一个请求来获得当前的缩放等级，`callback`将会被`callback(zoomLevel)`调用。

<h4 id="contents-setZoomLevelLimits"><code>contents.setZoomLevelLimits(minimumLevel, maximumLevel)</code></h4>

 * `minimumLevel` Number类型
 * `maximumLevel` Number类型

不赞成：调用`setVisualZoomLevelLimits`代替设置视觉上的缩放等级上限。这个方法将会在Electron 2.0中被移除。

<h4 id="contentssetVisualZoomLevelLimits-"><code>contents.setVisualZoomLevelLimits(minimumLevel, maximumLevel)</code></h4>

 * `minimumLevel` Number类型
 * `maximumLevel` Number类型

设置pinch-to-zoom等级的最大值和最小值。

<h4 id="contents-setLayoutZoomLevelLimits"><code>contents.setLayoutZoomLevelLimits(minimumLevel, maximumLevel)</code></h4>

 * `minimumLevel` Number类型
 * `maximumLevel` Number类型

设置布局基础（非视觉上的）缩放级别的最大值和最小值。

<h4 id="contents-undo"><code>contents.undo()</code></h4>

在网页中执行编辑命令`undo`。

<h4 id="contents-redo"><code>contents.redo()</code></h4>

在网页中执行编辑命令`redo`。

<h4 id="contents-cut"><code>contents.cut()</code></h4>

在网页中执行编辑命令`cut`。

<h4 id="contents-copy"><code>contents.copy()</code></h4>

在网页中执行编辑命令`copy`。

<h4 id="contents-copyImageAt"><code>contents.copyImageAt(x, y)</code></h4>

 * `x` Integer类型
 * `y` Integer类型

复制给定的定位的图片到剪贴板。

<h4 id="contents-paste"><code>contents.paste()</code></h4>

在网页中执行编辑命令`paste`。

<h4 id="contents-pasteAndMatchStyle"><code>contents.pasteAndMatchStyle()</code></h4>

在网页中执行编辑命令`pasteAndMatchStyle`。

<h4 id="contents-delete"><code>contents.delete()</code></h4>

在网页中执行编辑命令`delete`。

<h4 id="contents-selectAll"><code>contents.selectAll()</code></h4>

在网页中执行编辑命令`selectAll`。

<h4 id="contents-unselect"><code>contents.unselect()</code></h4>

在网页中执行编辑命令`unselect`。

<h4 id="contents-replace"><code>contents.replace(text)</code></h4>

 * `text` String类型

在网页中执行编辑命令`replace`。

<h4 id="contents-replaceMisspelling"><code>contents.replaceMisspelling(text)</code></h4>

 * `text` String类型

在网页中执行编辑命令`replaceMisspelling`。

<h4 id="contents-insertText"><code>contents.insertText(text)</code></h4>

 * `text` String类型

插入`text`到获取焦点的元素。

<h4 id="contents-findInPage"><code>contents.findInPage(text[, options])</code></h4>

 * `text` String类型 - 要搜索的内容，不能为空。
 * `options` Object类型（可选参数）
     * `forward` Boolean类型 -（可选参数）是否向前或向后搜索，默认为`true`。
     * `findNext` Boolean类型 -（可选参数）这个请求是首次请求还是跟随上一次请求，默认为`false`。
     * `matchCase` Boolean类型 -（可选参数）搜索是否区别大小写，默认为`false`。
     * `wordStart` Boolean类型 -（可选参数）是否只从单词开始查找，默认为`false`。
     * `medialCapitalAsWordStart` Boolean类型 -（可选参数） 当结合`wordStart`，接受一个在单词中间的匹配项，如果这个匹配项以一个大写字母开头后面有一个或没有小写字母。接受几个其他的字内匹配项，默认为`false`。

发起一个请求来寻找这个网页内的所有`text`的匹配项，并且返回代表这个请求的一个`Integer`类型的请求ID。这个请求的结果可以通过订阅`found-in-page`事件来获得。

<h4 id="contents-stopFindInPage"><code>contents.stopFindInPage(action)</code></h4>

 * `action` String类型 - 当要结束[`webContents.findInPage`]请求时指定一个活动来停止。
     * `clearSelection` - 清除选中。
     * `keepSelection` - 将选择转换成一个正常的选择。
     * `activateSelection` - 聚焦并点击选择的节点。

通过提供的`action`来停止任何对`webContents`的`findInPage`请求。

    const {webContents} = require('electron')
    webContents.on('found-in-page', (event, result) => {
        if (result.finalUpdate) webContents.stopFindInPage('clearSelection')
    })

    const requestId = webContents.findInPage('api')
    console.log(requestId)

<h4 id="contents-capturePage"><code>contents.capturePage([rect, ]callback)</code></h4>

 * `rect` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型（可选参数）- 要捕获的页面的区域
 * `callback` Function类型
     * `image` [NativeImage](https://github.com/electron/electron/blob/master/docs/api/native-image.md)类型

捕获一个在页面`rect`范围内的快照。当上面的方法完成后`callback`将会被`callback(image)`调用。这个`image`是一个存储了快照的`NativeImage`类型的实例。省略`rect`将会捕获整个可见的页面。

<h4 id="contents-hasServiceWorker"><code>contents.hasServiceWorker(callback)</code></h4>

 * `callback` Function类型
     * `hasWorker` Boolean类型

检测到如果有任何的ServiceWorker被注册那么久返回一个`boolean`类型的返回值给`callback`。

<h4 id="contents-unregisterServiceWorker"><code>contents.unregisterServiceWorker(callback)</code></h4>

 * `callback` Function类型
     * `success` Boolean类型

注销任何现存的ServiceWorker，并且返回一个`boolean`类型的返回值给`callback`，当JS的promise被满足返回`true`，或者JS的promise被拒绝则返回`false`。

<h4 id="contents-print"><code>contents.print([options])</code></h4>

 * `options` Object类型（可选参数）
     * `silent` Boolean类型 - 不询问用户的打印设置。默认为`false`。
     * `printBackground` Boolean类型 - 也打印网页的背景颜色和图片。默认是`false`。

打印窗口的网页。当`silent`被设置为`true`，Electron将得到系统的默认打印机和打印的默认设置。

在页面调用`window.print()`相当于调用`webContents.print({silent: false, printBackground: false})`。

使用`page-break-before: always;`CSS样式被强制打印在一个新的页面上。

<h4 id="contents-printToPDF"><code>contents.printToPDF(options, callback)</code></h4>

 * `options` Object类型
     * `marginsType` Integer类型 -（可选参数）指定使用的边距类型，使用0为默认边距，1为没有边距，2为最小边距。
     * `pageSize` String类型 -（可选参数）指定生成的PDF的页面尺寸。可以是`A3`、`A4`、`A5`、`Legal`、`Letter`、`Tabloid`或者一个包含了使用微米为单位的宽和高的对象。
     * `printBackground` Boolean类型 -（可选参数）是否打印CSS背景。
     * `printSelectionOnly` Boolean类型 -（可选参数）是否只打印选中项。
     * `landscape` Boolean类型 -（可选参数）`true`是landscape，`false`是portrait。
 * `callback` Function类型
     * `error` Error类型
     * `data` Buffer类型

使用Chromium的预览打印自定义设置打印窗口的网页为PDF。

完成后`callback`将会被`callback(error, data)`调用。`data`是一个包含了生成的PDF数据的`Buffer`类型。

如果CSS的`@page`规则被使用在这个网页中，那么`landscape`将会被忽略。

默认情况下，一个空的`options`奖杯视为：

    {
        marginsType: 0,
        printBackground: false,
        printSelectionOnly: false,
        landscape: false
    }

使用`page-break-before: always;`CSS样式被强制打印在一个新的页面上。

一个`webContents.printToPDF`的例子：

    const {BrowserWindow} = require('electron')
    const fs = require('fs')

    let win = new BrowserWindow({width: 800, height: 600})
    win.loadURL('http://github.com')

    win.webContents.on('did-finish-load', () => {
        // Use default printing options
        win.webContents.printToPDF({}, (error, data) => {
            if (error) throw error
            fs.writeFile('/tmp/print.pdf', data, (error) => {
                if (error) throw error
                console.log('Write PDF successfully.')
            })
        })
    })

<h4 id="contents-addWorkSpace"><code>contents.addWorkSpace(path)</code></h4>

 * `path` String类型

给开发工具工作空间添加指定路径。必须在开发工具创建完成后使用：

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow()
    win.webContents.on('devtools-opened', () => {
        win.webContents.addWorkSpace(__dirname)
    })

<h4 id="contents-removeWorkSpace"><code>contents.removeWorkSpace(path)</code></h4>

 * `path` String类型

从开发工具工作空间中移除指定的路径。

<h4 id="contents-openDevTools"><code>contents.openDevTools([options])</code></h4>

 * `options` Object类型（可选参数）
     * `mode` String类型 - 使用指定的dock状态打开开发工具，可以是`right`、`bottom`、`undocked`、`detach`。默认是上一次使用的dock状态。在`undocked`模式中它可能使dock返回。在`detach`模式中它则不是。

打开开发工具。

<h4 id="contents-closeDevTools"><code>contents.closeDevTools()</code></h4>

关闭开发工具。

<h4 id="contents-isDevToolsOpened"><code>contents.isDevToolsOpened()</code></h4>

返回值为`Boolean`类型 - 开发工具是否被打开。

<h4 id="contents-toggleDevTools"><code>contents.toggleDevTools()</code></h4>

使用开发工具。

<h4 id="contents-inspectElement"><code>contents.inspectElement(x, y)</code></h4>

 * `x` Integer类型
 * `y` Integer类型

开始检查点(`x`, `y`)位置的元素。

<h4 id="contents-inspectServiceWorker"><code>contents.inspectServiceWorker()</code></h4>

Opens the developer tools for the service worker context.

<h4 id="contents-send"><code>contents.send(channel[, arg1][, arg2][, ...])</code></h4>

 * `channel` String
 * `...args` any[]

通过`channel`来发送一个异步消息给渲染进程，你也可以发送任意类型的y参数。参数将在JSON内部被联播，因此没有函数或者原型链被引用。

渲染进程可以使用`ipcRenderer`模块通过监听`channel`来处理消息。

一个从主进程发送消息到渲染线程的例子：

    // In the main process.
    const {app, BrowserWindow} = require('electron')
    let win = null

    app.on('ready', () => {
        win = new BrowserWindow({width: 800, height: 600})
        win.loadURL(`file://${__dirname}/index.html`)
        win.webContents.on('did-finish-load', () => {
            win.webContents.send('ping', 'whoooooooh!')
        })
    })


    <!-- index.html -->
    <html>
    <body>
        <script>
            require('electron').ipcRenderer.on('ping', (event, message) => {
                console.log(message)  // Prints 'whoooooooh!'
            })
        </script>
    </body>
    </html>

<h4 id="contents-enableDeviceEmulation"><code>contents.enableDeviceEmulation(parameters)</code></h4>

 * `parameters` Object类型
     * `screenPosition` String类型 - 设置要模拟的屏幕类型（默认：`desktop`）
         * `desktop` - 桌面屏幕类型
         * `mobile` - 手机屏幕类型
     * `screenSize` Object类型 - 设置模拟屏幕的尺寸（`screenPosition`==`mobile`）
         * `width` Integer类型 - 设置模拟屏幕的宽度
         * `height` Integer类型 - 设置模拟屏幕得高度
     * `viewPosition` Object类型 - 屏幕中视图的位置（`screenPosition`==`mobile`）（默认：`{x: 0, y: 0}`）
         * `x` Integer类型 - 设置左上角x轴的偏移值
         * `y` Integer类型 - 设置左上角y轴的偏移值
     * `deviceScaleFactor` Integer类型 - 设置设备的缩放因素（如果原始设备缩放因素默认为0）（默认：`0`）
     * `viewSize` Object类型 - 设置模拟视图的尺寸（为空则意味着不会覆盖）
         * `width` Integer类型 - 设置模拟视图的宽度
         * `height` Integer类型 - 设置模拟视图的高度
     * `fitToView` Boolean类型 - 模拟视图是否需要缩放到适配有效空间（默认：`false`）
     * `offset` Object类型 - 模拟视图内部可用空间的偏移量（不适合视图模式）（默认：`{x: 0, y: 0}`）
         * `x` Float类型 - 设置左上角x轴的偏移值
         * `y` Float类型 - 设置左上角y轴的偏移值
     * `scale` Float类型 - 缩放模拟视图内部可用空间（不适合视图模式）（默认：`1`）

通过给定的参数允许设备模拟

<h4 id="contents-disableDeviceEmulation"><code>contents.disableDeviceEmulation()</code></h4>

关闭通过`webContents.enableDeviceEmulation`开启的设备模拟。

<h4 id="contents-sendInputEvent"><code>contents.sendInputEvent(event)</code></h4>

 * `event` Object类型
     * `type` String类型（必传参数）- 事件的类型，可以是`mouseDown`、`mouseUp`、`mouseEnter`、`mouseLeave`、`contextMenu`、`mouseWheel`、`mouseMove`、`keyDown`、`keyUp`、`char`。
     * `modifiers` String[]类型 - 一个事件的调节器数组，可以包含`shift`、`control`、`alt`、`meta`、`isKeypad`、`isAutoRepeat`、`leftButtonDown`、`middleButtonDown`、`rightButtonDown`、`capsLock`、`numLock`、`left`、`right`。

发送一个输入事件到页面。

对于键盘事件，`event`对象也有以下属性：

 * `keyCode` String（必传参数）- 字符将被作为键盘事件而发送。只能使用[Accelerator](https://github.com/electron/electron/blob/master/docs/api/accelerator.md)中的键码值。

对于鼠标事件，`event`对象也有以下属性：

 * `x` Integer类型（必传参数）
 * `y` Integer类型（必传参数）
 * `button` String类型 - 按下的按钮，可以是`left`、`middle`、`right`
 * `globalX` Integer类型
 * `globalY` Integer类型
 * `movementX` Integer类型
 * `movementY` Integer类型
 * `clickCount` Integer类型

对于鼠标滚轮事件，`event`对象也有以下属性：

 * `deltaX` Integer类型
 * `deltaY` Integer类型
 * `wheelTicksX` Integer类型
 * `wheelTicksY` Integer类型
 * `accelerationRatioX` Integer类型
 * `accelerationRatioY` Integer类型
 * `hasPreciseScrollingDeltas` Boolean类型
 * `canScroll` Boolean类型


<h4 id="contents-beginFrameSubscription"><code>contents.beginFrameSubscription([onlyDirty ,]callback)</code></h4>

 * `onlyDirty` Boolean类型（可选参数）- 默认值为`false`
 * `callback` Function类型
     * `frameBuffer` Buffer类型
     * `dirtyRect` Rectangle类型

开始订阅报告事件和捕获frames，当有一个报告事件时`callback`会被`callback(frameBuffer, dirtyRect)`调用。

`frameBuffer`是一个包含原始像素数据的`Buffer`。在大多数机器上，像素数据实际上被存储为32位BGRA格式，但是真实显示取决于中央处理器的字节顺序（大多数现代中央处理器是little-endian，在big-endian中央处理器的机器上数据是32位ARGB格式）。

`dirtyRect`是带有`x, y, width, height`这些属性的对象，来描述页面的重绘部分。如果`onlyDirty`被设置为`true`，`frameBuffer`将只会包含重绘区域。`onlyDirty`默认为`false`。

<h4 id="contents-endFrameSubscription"><code>contents.endFrameSubscription()</code></h4>

结束订阅frame报告事件。

<h4 id="contents-startDrag"><code>contents.startDrag(item)</code></h4>

 * `item` Object类型
     * `file` 字符串或者`files`类型的数组 - 开始拖拽的文件的路径。
     * `icon` [NativeImage](https://github.com/electron/electron/blob/master/docs/api/native-image.md)类型 - MacOS下这个图片不可以为空。

设置这个`item`作为当前拖拽操作的拖拽项，`file`是被拖拽文件的绝对路径，`icon`是当拖拽时光标下方展示的图片。

<h4 id="contents-savePage"><code>contents.savePage(fullPath, saveType, callback)</code></h4>

 * `fullPath` String类型 - 文件的完整路径。
 * `saveType` String类型 - 指定保存类型。
     * `HTMLOnly` - 只保存当前页面为HTML。
     * `HTMLComplete` - 保存完整的HTML页面。
     * `MHTML` - 保存完整的HTML页面为MHTML。
 * `callback` Function类型 - (error) => {}.
     * `error` Error类型

返回值为`Boolean`类型 - `true`表示保存页面的过程已经成功开始。

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow()

    win.loadURL('https://github.com')

    win.webContents.on('did-finish-load', () => {
        win.webContents.savePage('/tmp/test.html', 'HTMLComplete', (error) => {
            if (!error) console.log('Save page successfully')
        })
    })

<h4 id="contents-showDefinitionForSelection"><code>contents.showDefinitionForSelection() <i>（MacOS）</i></code></h4>

展示用来在页面上对选中的单词进行搜索的弹出式字典。

<h4 id="contents-setSize"><code>contents.setSize(options)</code></h4>

设置页面的尺寸。这个只支持<webview>的guest内从。

 * `options` Object类型
     * `normal` Object类型（可选参数）- 页面的正常大小。这个可以被用在与[`disableguestresize`](https://github.com/electron/electron/blob/master/docs/api/web-view-tag.md#disableguestresize)参数联合，来手动调整webview的guest内容的大小。
         * `width` Integer类型
         * `height` Integer类型

<h4 id="contents-isOffscreen"><code>contents.isOffscreen()</code></h4>

返回值为`Boolean`类型 - 表示是否启用*离屏渲染*。

<h4 id="contents-startPainting"><code>contents.startPainting()</code></h4>

如果*离屏渲染*被启动但是没有开始绘制，那么就让它开始绘制。

<h4 id="contents-stopPainting"><code>contents.stopPainting()</code></h4>

如果*离屏渲染*被启动并且正在绘制，那么就让它停止绘制。

<h4 id="contents-isPainting"><code>contents.isPainting()</code></h4>

返回值为`Boolean`类型 - 如果*离屏渲染*被启动则返回当前是否正在绘制。

<h4 id="contents-setFrameRate"><code>contents.setFrameRate(fps)</code></h4>

 * `fps` Integer类型

如果*离屏渲染*被启动那么设置帧数为指定的数值。只接受在1到60之间的值。

<h4 id="contents-getFrameRate"><code>contents.getFrameRate()</code></h4>

返回值为`Integer`类型 - 如果*离屏渲染*被启动那么返回当前帧数。

<h4 id="contents-invalidate"><code>contents.invalidate()</code></h4>

安排一次这个网络内容所在的窗口的全屏重绘。

如果*离屏渲染*被启那么使frame无效并且通过`'paint'`事件生成一个新的。

<h3 id="instance-properties">实例属性</h3>

<h4 id="contents-id"><code>contents.id</code></h4>

一个作为WebContents的唯一的ID的整数。

<h4 id="contents-session"><code>contents.session</code></h4>

一个被这个webContents使用的Session对象（[session](https://github.com/electron/electron/blob/master/docs/api/session.md)）

<h4 id="contents-hostWebContents"><code>contents.hostWebContents</code></h4>

一个可能拥有这个WebContents的[`WebContents`](https://github.com/electron/electron/blob/master/docs/api/web-contents.md)实例

<h4 id="contents-devToolsWebContents"><code>contents.devToolsWebContents</code></h4>

这个WebContents的开发工具的WebContents。

**注意：**用户不要存储这个对象，因为当这个开发工具被关闭的时候它可能会变为`null`。

<h4 id="contents-debugger"><code>contents.debugger</code></h4>

这个webContents的一个[Debugger](https://github.com/electron/electron/blob/master/docs/api/debugger.md)实例。
