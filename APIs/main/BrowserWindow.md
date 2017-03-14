# 3. BrowserWindow

> 原文：https://github.com/electron/electron/blob/master/docs/api/browser-window.md   
译者：[Lin](https://github.com/ShmilyLin)   

> 创建并且控制浏览器窗口。

进程：[主进程](../../guides/glossary-of-terms.md#main-process)   

    // 主进程中
    const {BrowserWindow} = require('electron')

    // 或者使用渲染进程中的`remote`
    // const {BrowserWindow} = require('electron').remote

    let win = new BrowserWindow({width: 800, height: 600})
    win.on('closed', () => {
        win = null
    })

    // 加载网络地址
    win.loadURL('https://github.com')

    // 加载一个本地HTML文件
    win.loadURL(`file://${__dirname}/app/index.html`)

<h2 id="frameless-window">无框架窗口</h2>

创建一个没有chrome的窗口，或者一个任意形状的透明窗口，你可以使用[Frameless Window](https://github.com/electron/electron/blob/master/docs/api/frameless-window.md)接口。   

<h2 id="showing-window-gracefully">优雅的展示窗口</h2>

当直接在窗口中加载一个页面，用户可能看到页面逐渐加载的过程，这对于原生应用来说不是一个很好的体验。为了使窗口没有视觉上的闪烁，针对不同的情况这里又两种解决方案。

<h3 id="using-ready-to-show-event">使用<code>ready-to-show</code>事件</h3>

页面加载的时候，渲染进程已经开始首次绘制时`ready-to-show`事件将会被分发，在这个事件之后展示窗口将不会出现视觉上的闪烁：   

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow({show: false})
    win.once('ready-to-show', () => {
        win.show()
    })

这个事件通常在`did-finish-load`事件之后被分发，但是包含了很多远程资源的页面可能会在`did-finish-load`事件之前被分发。   

<h3 id="setting-backgroundColor">设置<code>backgroundColor`</code></h3>

在一个复杂的应用中，`ready-to-show`事件可能很晚才会被分发，这会使得应用感觉很慢。这种情况下，建议立刻展示窗口，使用一个`backgroundColor`覆盖住你应用的背景：

    const {BrowserWindow} = require('electron')

    let win = new BrowserWindow({backgroundColor: '#2e2c29'})
    win.loadURL('https://github.com')

值得注意的是，即使应用使用了`ready-to-show`事件，也依然建议设置`backgroundColor`来使得应用更像原生应用。

<h2 id="parent-and-child-windows">父窗口和子窗口</h2>

通过使用`parent`选项，你可以创建一个子窗口：

    const {BrowserWindow} = require('electron')

    let top = new BrowserWindow()
    let child = new BrowserWindow({parent: top})
    child.show()
    top.show()

这个`child`窗口将总展示在`top`窗口的上面。

<h3 id="modal-windows">模态窗口</h3>

一个模态窗口是一个禁用父窗口的子窗口，创建一个模态窗口，你需要设置`parent`和`modal`选项：

    const {BrowserWindow} = require('electron')

    let child = new BrowserWindow({parent: top, modal: true, show: false})
    child.loadURL('https://github.com')
    child.once('ready-to-show', () => {
      child.show()
    })

<h3 id="platform-notices">平台的注意事项</h3>

 * MacOS中，模态窗口将被作为附加页展示在父窗口中。   
 * MacOS中，当父窗口移动时子窗口将与父窗口保持相对的位置，而在Windows和Linux中子窗口将不会移动。   
 * Windows中， 不支持动态的改变父窗口。   
 * Linux中，模态窗口的类型将会变为对话框。   
 * Linux中，多数桌面环境不支持隐藏一个模态窗口。

<h2 id="class-browserWindow">类：BrowserWindow</h2>

> 创建并且控制浏览器窗口。   

进程：[主进程](../../guides/glossary-of-terms.md#main-process)    

`BrowserWindow`是一个[EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)。   

它使用`options`设置的本地属性的创建一个新的`BrowserWindow`。

<h3 id="new-browserWindow"><code>new BrowserWindow([options])</code></h3>

 * `options` Object类型（可选参数）   
     * `width` Integer类型（可选参数）- 以像素为单位的窗口的宽度。默认是`800`。   
     * `height` Integer类型（可选参数）- 以像素为单位的窗口的高度。默认是`600`。   
     * `x` Integer类型（可选参数）（如果使用`y`参数，则必须有该参数）- 窗口基于屏幕的左侧偏移量。默认是窗口居中。   
     * `y` Integer类型（可选参数）（如果使用`x`参数，则必须有该参数）- 窗口基于屏幕的顶部偏移量。默认是窗口居中。   
     * `useContentSize` Boolean类型（可选参数）- `width`和`height`是否直接作用于网页的大小，如果`true`则意味着真实的窗口大小将包含窗口边框的大小并且比它略大。默认是`false`。   
     * `center` Boolean类型（可选参数）- 是否在屏幕中间展示窗口。   
     * `minWidth` Integer类型（可选参数）- 窗口的最小宽度。默认为`0`。   
     * `minHeight` Integer类型（可选参数）- 窗口的最小高度。默认为`0`。   
     * `maxWidth` Integer类型（可选参数）- 窗口的最大宽度。默认为没有限制。   
     * `maxHeight` Integer类型（可选参数）- 窗口的最大高度。默认为没有限制。   
     * `resizable` Boolean类型（可选参数）- 是否可以调整窗口大小。默认为`true`。   
     * `movable` Boolean类型（可选参数）- 窗口是否可以移动。这个参数在Linux中是无效的。默认为`true`。    
     * `minimizable` Boolean类型（可选参数）- 窗口是否可以最小化。这个参数在Linux中是无效的。默认为`true`。   
     * `maximizable` Boolean类型（可选参数）- 窗口是否可以最大化。这个参数在Linux中是无效的。默认为`true`。   
     * `closable` Boolean类型（可选参数）- 窗口是否允许关闭。这个参数在Linux中是无效的。默认为`true`。   
     * `focusable` Boolean类型（可选参数）- 窗口是否可以聚焦。默认为`true`。Windows中设置`focusable: false` 也代表着设置`skipTaskbar: true`。Linux中设置`focusable: false`会使得窗口停止与wm的交互，所以窗口将总保持在所有工作空间的顶部。   
     * `alwaysOnTop` Boolean类型（可选参数）- 窗口是否总保持在其他窗口的上面。默认是`false`。   
     * `fullscreen` Boolean类型（可选参数）- 窗口是否需要全屏展示。MacOS中，当显式的设置为`false`，全屏按钮将会被隐藏。默认为`false`。
     * `fullscreenable` Boolean类型（可选参数）- 窗口是否可以进入全屏模式。MacOS中，也代表着最大化／放大按钮是否可以切换全屏或最大化。默认是`true`。
     * `skipTaskbar` Boolean类型（可选参数）- 是否在任务栏上显示窗口。默认为`false`。
     * `kiosk` Boolean类型（可选参数）- kiosk模式（锁定运行模式）。默认为`false`。   
     * `title` String类型（可选参数）- 窗口的默认标题。默认为`"Electron"`。   
     * `icon` ([NativeImage](../both/nativeImage.md) | String)类型（可选参数）- 窗口的图标。Windows中建议使用`ICO`格式的图标来获得最好的视觉效果，你也可以不设置这样executable的图标就会默认的被设置。
     * `show` Boolean类型（可选参数）- 是否在创建时显示窗口。默认为`true`。   
     * `frame` Boolean类型（可选参数）- 设置为`false`来创建一个[Frameless Window](https://github.com/electron/electron/blob/master/docs/api/frameless-window.md)。默认为`true`。
     * `parent` BrowserWindow类型（可选参数）- 制定父窗口。默认为`null`。   
     * `modal` Boolean类型（可选参数）- 是否是一个模态窗口。这个属性只有在当前窗口是子窗口时工作。默认为`false`。
     * `acceptFirstMouse` Boolean类型（可选参数）- 网页试图是否接受一个单击事件同时激活窗口。默认为`false`。   
     * `disableAutoHideCursor` Boolean类型（可选参数）- 是否在输入时隐藏光标。默认为`false`。   
     * `autoHideMenuBar` Boolean类型（可选参数）- 除非按下`Alt`键否则自动隐藏菜单栏。默认为`false`。   
     * `enableLargerThanScreen` Boolean类型（可选参数）- 允许窗口缩放大于屏幕。默认为`false`。   
     * `backgroundColor` String类型（可选参数）- 使用一个十六进制的值来设置窗口的背景颜色，例如`#66CD00`或者`#FFF`或者`80FFFFFF`（支持透明度）。默认是`#FFF`（白色）。   
     * `hasShadow` Boolean类型（可选参数）- 窗口是否需要有一个阴影。这个属性仅在MacOS下有效。默认为`true`。   
     * `darkTheme` Boolean类型（可选参数）- 窗口强制使用黑色主题，仅在一些GTK+3桌面环境中有效。默认为`false`。   
     * `transparent` Boolean类型（可选参数）- 使窗口[透明](https://github.com/electron/electron/blob/master/docs/api/frameless-window.md)。默认为`false`。   
     * `type` String类型（可选参数）- 窗口的类型，默认是一般窗口。在下面查看更多信息。   
     * `titleBarStyle` String类型（可选参数）- 窗口标题栏风格。默认是`default`。可选的值有：   
         * `default` - 结果是标准灰色不透明Mac标题栏。
         * `hidden` - 结果是隐藏标题栏和一个全尺寸的内容窗口，但是标题栏在左上角仍有标准的窗口控制（“交通灯”）。
         * `hidden-inset` - 结果是一个看起来非正规的隐藏的标题栏，交通灯按钮稍微的更接近窗口边缘。
     * `thickFrame` Boolean类型（可选参数）- Windows中无边框窗口使用`WS_THICKFRAME`风格，将会添加一个标准的窗口边框。设置`false`将会移除窗口阴影和窗口动画。默认为`true`。
     * `vibrancy` String类型（可选参数）- 为窗口添加一个震动效果，仅在MacOS中有效。可以是`appearance-based`，`light`，`dark`，`titlebar`，`selection`，`menu`，`popover`，`sidebar`，`medium-light`或者`ultra-dark`。
     * `zoomToPageWidth` Boolean类型（可选参数）- MacOS中控制当单击工具栏的绿色信号灯按钮或点击窗口放大菜单项时的行为。如果设置为`true`，当缩放窗口时窗口将会逐渐扩大到首选宽度，如果设置为`false`则会导致缩放到屏幕宽度。这个参数也将影响到直接调用`maximize()`的结果。默认为`false`。
     * `webPreferences` Object类型（可选参数）- 设置网页的特征。
         * `devTools` Boolean类型（可选参数）- 是否启用开发工具。如果设置为`false`，将不能使用`BrowserWindow.webContents.openDevTools()`来打开开发工具。默认为`true`。
         * `nodeIntegration` Boolean类型（可选参数）- 是否启用node完整性。默认为`true`。
         * `preload` String类型（可选参数）- 制定一个脚本在这个页面中的其他脚本家在之前加载。这个脚本将有权使用node接口，无论`nodeIntegration`是否开启。这个属性的值必须是脚本文件的绝对路径。当`nodeIntegration`设为`false`时，这个预加载脚本可以从全局范围内重新引用Node全局标识符。[例子](../process.md#event-loaded)请看这里。
         * `session` [Session](./session.md#class-session)类型（可选参数）- 设置页面使用的会话。Instead of passing the Session object directly，你也可以选择使用`partition`选项代替，which accepts a partition string. 当`session`和`partition`都被提供，`session`将会被作为首选。默认是default session。
         * `partition` String类型（可选参数）- Sets the session used by the page according to the session's partition string. If partition starts with persist:, the page will use a persistent session available to all pages in the app with the same partition. If there is no persist: prefix, the page will use an in-memory session. By assigning the same partition, multiple pages can share the same session. Default is the default session.
         * `zoomFactor` Number类型（可选参数）- 页面的默认缩放因子，`3.0`表示`300%`，默认为`1.0`。   
         * `javascript` Boolean类型（可选参数）- 能够支持JavaScript。默认是`true`。   
         * `webSecurity` Boolean类型（可选参数）- 当设置为`false`，它将禁用同源策略（人们一般用来测试网站），并且如果这个选项没有被设置那么将设置`allowRunningInsecureContent`为`true`。默认为`true`。
         * `allowRunningInsecureContent` Boolean类型（可选参数）- 允许一个https页面运行JavaScript，CSS或者http URLs的插件。默认为`false`。
         * `images` Boolean类型（可选参数）- 启用图片支持。默认为`true`。
         * `textAreasAreResizable` Boolean类型（可选参数）- 使TextArea元素可以调整大小。默认为`true`。
         * `webgl` Boolean类型（可选参数）- 开启WebGL支持。默认为`true`。
         * `webaudio` Boolean类型（可选参数）- 开启WebAudio支持。默认为`true`。
         * `plugins` Boolean类型（可选参数）- 插件是否被允许开启。默认为`false`。
         * `experimentalFeatures` Boolean类型（可选参数）- 开启Chromium的试验功能。默认为`false`。
         * `experimentalCanvasFeatures` Boolean类型（可选参数）- 开启Chromium的canvas试验功能。默认为`false`。
         * `scrollBounce` Boolean类型（可选参数）- MacOS下开启弹性滚动（橡胶带）。默认为`false`。
         * `blinkFeatures` String类型（可选参数）- 一个使用`,`分隔的字符串表示的功能列表，例如`CSSVariables,KeyboardEventKey`，开启这个功能列表。完整的支持功能列表字符串可以在[RuntimeEnabledFeatures.json5](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.json5?l=62)文件中找到。
         * `disableBlinkFeatures` String类型（可选参数）- 一个使用`,`分隔的字符串表示的功能列表，例如`CSSVariables,KeyboardEventKey`，不开启这个功能列表。完整的支持功能列表字符串可以在[RuntimeEnabledFeatures.json5](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.json5?l=62)文件中找到。
         * `defaultFontFamily` Object类型（可选参数）- 从字体族中设置字体。
             * `standard` String类型（可选参数）- 标准，默认是`Times New Roman`。
             * `serif` String类型（可选参数）- 衬线体，默认是`Times New Roman`。
             * `sansSerif` String类型（可选参数）- 滑体，默认是`Arial`。
             * `monospace` String类型（可选参数）- 等宽，默认是`Courier New`。
             * `cursive` String类型（可选参数）- 草书，默认是`Script`。
             * `fantasy` String类型（可选参数）- 默认是`Impact`。
         * `defaultFontSize` Integer类型（可选参数）- 字体大小，默认是`16`。
         * `defaultMonospaceFontSize` Integer类型（可选参数）- 默认等宽字体大小，默认是`13`。
         * `minimumFontSize` Integer类型（可选参数）- 最小字体大小，默认是`0`。
         * `defaultEncoding` String类型（可选参数）- 默认编码，默认是`ISO-8859-1`。
         * `backgroundThrottling` Boolean类型（可选参数）- 当页面变成背景时是否停止动画和计时器。默认是`true`。
         * `offscreen` Boolean类型（可选参数）- 是否启用浏览器窗口在屏幕外渲染。查看[离屏渲染教程](../../guides/offscreen-rendering.md)来获得详细信息。
         * `sandbox` Boolean类型（可选参数）- 是否开启Chromium系统级别的沙盒。
         * `contextIsolation` Boolean类型（可选参数）- 是否在一个隔离的Javascript环境中运行Ellectron接口和指定的预加载脚本。默认是`false`。Defaults to false. The context that the preload script runs in will still have full access to the document and window globals but it will use its own set of JavaScript builtins (Array, Object, JSON, etc.) and will be isolated from any changes made to the global environment by the loaded page. Electron接口将会仅在`preload`脚本中有效而不在已经加载的页面中有效。This option should be used when loading potentially untrusted remote content to ensure the loaded content cannot tamper with the preload script and any Electron APIs being used. 这个选项使用和[Chrome Content Scripts](https://developer.chrome.com/extensions/content_scripts#execution-environment)使用的相同的技术。你可以在开发工具中通过在控制台选项卡的顶部的组合框中选中'Electron Isolated Context'入口里获取context。**注意：**这个选项仅是当前的实验项，可能会在未来的Electron版本中改变或被移除。   

使用`minWidth`/`maxWidth`/`minHeight`/`maxHeight`设置窗口大小的最小值和最大值只会限制使用者。它并不会阻止你设置一个不遵循`setBounds`/`setSize`的和`BrowserWindow`的构造函数内的大小约束。  

`type`选项的可能值和对应的行为取决于运行的平台。可能值有：

 * Linux中，可能值有`desktop`，`dock`，`toolbar`，`splash`，`notification`。
 * MacOS中，可能值有`desktop`，`textured`。
     * `textured`类型添加一个金属纹路的外观（`NSTexturedBackgroundWindowMask`）。
     * `desktop`类型将窗口的位置放置在桌面背景等级上（`kCGDesktopWindowLevel - 1`）。请注意，桌面窗口将不会接收聚焦，键盘输入和鼠标事件，不过你可以使用`globalShortcut`来解决接受输入的问题。
 * Windows中，可能值是`toolbar`。

<h3 id="instance-events">对象的事件</h3>

使用`new BrowserWindow`创建的对象会分发以下事件：   

**注意：**一些事件只在特定的操作系统中有效，已经被标记出来。

<h4 id="event-page-title-updated">事件：‘page-title-updated’</h4>

返回值为：

 * `event` Event类型
 * `title` String类型

当文档改变它自己的标题时被分发，调用`event.preventDefault()`将阻止本地窗口的标题改变。

<h4 id="event-close">事件：‘close’</h4>

返回值为：

 * `event` Event类型

当窗口将要被关闭时被分发。它会在DOM的`beforeunload`和`unload`事件之前被分发。调用event.preventDefault()会取消关闭。

通常你需要使用`beforeunload`来处理是否关闭窗口的决定，当窗口重新加载的时候也会被调用。Electron中，返回除了`undefined`之外的任何值都将会取消关闭。例如：

   window.onbeforeunload = (e) => {
      console.log('我不想要被关闭')

      // 不同于其他浏览器提供给用户一个消息盒子，返回一个非空值将会默默的取消关闭。
      // 建议使用dialog接口来让用户确认关闭应用。
      e.returnValue = false
   }

<h4 id="event-closed">事件：‘closed’</h4>

当窗口被关闭的时候被分发。你收到这个事件之后你需要移除关于这个窗口的引用，并且避免再次使用它。

<h4 id="event-unresponsive">事件：‘unresponsive’</h4>

当网页变成无应答状态时被分发。

<h4 id="event-responsive">事件：‘responsive’</h4>

当无应答的网页再次有反应时被分发。

<h4 id="event-blur">事件：‘blur’</h4>

当焦点不在窗口上时被分发。

<h4 id="event-focus">事件：‘focus’</h4>

当窗口获得焦点的时候被分发。

<h4 id="event-show">事件：‘show’</h4>

当窗口被展示的时候被分发。

<h4 id="event-hide">事件：‘hide’</h4>

当窗口被隐藏的时候被分发。

<h4 id="event-ready-to-show">事件：‘ready-to-show’</h4>

当网页已经被渲染完成，可以在没有视觉上的闪烁的情况下被展示的时候被分发。

<h4 id="event-maximize">事件：‘maximize’</h4>

窗口最大化的时候被分发。

<h4 id="event-unmaximize">事件：‘unmaximize’</h4>

窗口退出最大化状态时被分发。

<h4 id="event-minimize">事件：‘minimize’</h4>

窗口最小化的时候被分发。

<h4 id="event-restore">事件：‘restore’</h4>

当窗口从最小化状态被恢复的时候被分发。

<h4 id="event-resize">事件：‘resize’</h4>

当窗口开始被调整大小的时候被分发。

<h4 id="event-move">事件：‘move’</h4>

窗口开始被移动到新的位置的时候被分发。

**注意：**MacOS中这个事件仅仅是`moved`事件的一个别名。

<h4 id="event-moved">事件：‘moved’ <i>（MacOS）</i></h4>

当窗口已经被移动到新的位置的时候被分发一次。

<h4 id="event-enter-full-screen">事件：‘enter-full-screen’</h4>

当窗口进入全屏状态的时候被分发。

<h4 id="event-leave-full-screen">事件：‘leave-full-screen’</h4>

当窗口从全屏状态中推出的时候被分发。

<h4 id="event-enter-html-full-screen">事件：‘enter-html-full-screen’</h4>

当窗口因为HTML接口而进入全屏状态的时候被分发。

<h4 id="event-leave-html-full-screen">事件：‘leave-html-full-screen’</h4>

当窗口因为HTML接口而从全屏状态中退出的时候被分发。

<h4 id="event-app-command">事件：‘app-command’ <i>（Windows）</i></h4>

返回值为：

 * `event` Event类型
 * `command` String类型

当一个[应用命令](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx)被调用的时候被分发。这些通常关系到键盘的媒体键或浏览器命令，以及Windows中一些鼠标上的“Back”按钮。

命令是小写字母组成的，下划线用于替代连字符，并且被去掉`APPCOMMAND_`前缀。例如`APPCOMMAND_BROWSER_BACKWARD`被作为一个浏览器后退命令分发。

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow()
    win.on('app-command', (e, cmd) => {
        // 当用户点鼠标上的后退按钮时窗口会后退导航
        if (cmd === 'browser-backward' && win.webContents.canGoBack()) {
            win.webContents.goBack()
        }
    })

<h4 id="event-scroll-touch-begin">事件：‘scroll-touch-begin’ <i>（MacOS）</i></h4>

当滚轮事件开始时被分发。

<h4 id="event-scroll-touch-end">事件：‘scroll-touch-end’ <i>（MacOS）</i></h4>

当滚轮事件结束时被分发。

<h4 id="event-scroll-touch-edge">事件：‘scroll-touch-edge’ <i>（MacOS）</i></h4>

当滚轮事件到达元素边缘的时候被分发。

<h4 id="event-swipe">事件：‘swipe’ <i>（MacOS）</i></h4>

返回值为：

 * `event` Event类型
 * `direction` String类型

当三个手指滑动时被分发。可能的方向有`up`，`right`，`down`，`left`。

<h3 id="static-methods">静态方法</h3>

`BrowserWindow`类有下面的这些静态方法：

<h4 id="BrowserWindow-getAllWindows">BrowserWindow.getAllWindows()</h4>

返回值为`BrowserWindow[]`类型 - 所有打开的浏览器窗口的数组。

<h4 id="BrowserWindow-getFocusedWindow">BrowserWindow.getFocusedWindow()</h4>

返回值为`BrowserWindow`类型 - 应用中获得焦点的窗口，如果没有则返回`null`。

<h4 id="BrowserWindow-fromWebContents">BrowserWindow.fromWebContents(webContents)</h4>

 * `webContents` WebContents类型

返回值为`BrowserWindow` - 通过给定的`webContents`返回指定的窗口。

<h4 id="BrowserWindow-fromId">BrowserWindow.fromId(id)</h4>

 * `id` Integer类型

返回值为`BrowserWindow`类型 - 通过给定的`id`返回指定的窗口。

<h4 id="BrowserWindow-addDevToolsExtension">BrowserWindow.addDevToolsExtension(path)</h4>

 * `path` String类型

添加位于路径中的开发工具扩展，并且返回扩展的名字。   

扩展将会被记录，所以你只需要调用这个接口一次就可以了。如果你尝试添加一个已经被加载的扩展，这个方法将没有任何返回，而是在控制台中输出一个警告。   

如果这个扩展的证书丢失或者缺失，这个方法也不会返回任何值。   

**注意：**这个接口不会在`app`模块中的`ready`事件被分发之前被调用。

<h4 id="BrowserWindow-removeDevToolsExtension">BrowserWindow.removeDevToolsExtension(name)</h4>

 * `name` String类型   

通过名字移除一个开发工具扩展。

**注意：**这个接口不会在`app`模块中的`ready`事件被分发之前被调用。

<h4 id="BrowserWindow-getDevToolsExtensions">BrowserWindow.getDevToolsExtensions()</h4>

返回值为`Object`类型 - 键是扩展的名称，对应的每一个值都是一个包含了`name`和`version`属性的对象。

你可以运行下面的代码来检查开发工具扩展是否被安装：

    const {BrowserWindow} = require('electron')

    let installed = BrowserWindow.getDevToolsExtensions().hasOwnProperty('devtron')
    console.log(installed)

**注意：**这个接口不会在`app`模块中的`ready`事件被分发之前被调用。

<h3 id="instance-properties">实例的属性</h3>

通过`new BrowserWindow`创建的对象具有下面的属性：

    const {BrowserWindow} = require('electron')
    // 在这个例子中“win”是我们的实例
    let win = new BrowserWindow({width: 800, height: 600})
    win.loadURL('https://github.com')

<h4 id="win-webContents">win.webContents</h4>

窗口拥有的一个`WebContents`对象。所有与网页有关的事件和操作都会通过它来完成。

查看[`webContents` documentation](./webContents.md)来了解它的方法和事件。

<h4 id="win-id">win.id</h4>

一个`Integer`类型的代表着窗口的唯一ID。

<h3 id="instance-methods">实例的方法</h3>

通过`new BrowserWindow`创建的对象具有下面的方法：

**注意：**一些方法只在特定的操作系统中有效，已经被标记出来。

<h4 id="win-destroy">win.destroy()</h4>

强制关闭窗口，网页的`unload`和`beforeunload`事件将不会被分发，窗口的`close`事件也不会被分发，但是它保证`closed`事件会被分发。

<h4 id="win-close">win.close()</h4>

尝试关闭窗口。这个和用户手动点击窗口的关闭按钮是一样的效果。网页可能会取消关闭。请查看[close事件](#event-close)。

<h4 id="win-focus">win.focus()</h4>

窗口获得焦点。

<h4 id="win-blur">win.blur()</h4>

窗口移除焦点。

<h4 id="win-isFocused">win.isFocused()</h4>

返回值为`Boolean`类型 - 窗口是否获得焦点。

<h4 id="win-isDestroyed">win.isDestroyed()</h4>

返回值为`Boolean`类型 - 窗口是否被销毁。

<h4 id="win-show">win.show()</h4>

展示窗口并且使窗口获得焦点。

<h4 id="win-showInactive">win.showInactive()</h4>

展示窗口但不给窗口焦点。

<h4 id="win-hide">win.hide()</h4>

隐藏窗口。

<h4 id="win-isVisible">win.isVisible()</h4>

返回值为`Boolean`类型 - 当前窗口对于用户是否是可见的。

<h4 id="win-isModal">win.isModal()</h4>

返回值为`Boolean`类型 - 当前窗口是否是一个模态窗口。

<h4 id="win-maximize">win.maximize()</h4>

最大化窗口。

<h4 id="win-unmaximize">win.unmaximize()</h4>

取消最大化窗口。

<h4 id="win-isMaximized">win.isMaximized()</h4>

返回值为`Boolean`类型 - 窗口是否最大化。

<h4 id="win-minimize">win.minimize()</h4>

最小化窗口。在一些平台上，最小化的窗口将会被展示在dock上。

<h4 id="win-restore">win.restore()</h4>

将最小化的窗口恢复到以前的状态。

<h4 id="win-isMinimized">win.isMinimized()</h4>

返回值为`Boolean`类型 - 窗口是否最小化。

<h4 id="win-setFullScreen">win.setFullScreen(flag)</h4>

 * `flag` Boolean类型

设置窗口是否进入全屏模式。

<h4 id="win-isFullScreen">win.isFullScreen()</h4>

返回值为`Boolean`类型 - 窗口是否在全屏模式下。

<h4 id="win-setAspectRatio">win.setAspectRatio (aspectRatio[, extraSize]) <i>（MacOS）</i></h4>

 * `aspectRatio` Float类型 - 保持的部分内容视图的纵横比。
 * `extraSize` Object类型（可选参数）- 不在保持的纵横比内的额外的尺寸。
     * `width` Integer类型
     * `height` Integer类型

这将使窗口保持一个纵横比。使用像素设置的额外尺寸将允许一个开发者有不包含在纵横比计算之内的空间。这个接口已经考虑到了不同窗口之间的大小和窗口的内容的大小。

考虑到一个HD视频播放器和相关控件的一般窗口。可能在左边框会有15像素的控件，有边框有25像素控件，播放器下方还会有50像素的控件。为了在播放器内保持它自己的`16:9`的纵横比（1920x1080HD的标准纵横比），我们需要调用这个方法，并传入参数`16/9`和`[40,50]`。第二个参数不关心额外的宽度和高度在内容视图的什么位置——只关心是否存在。只会在整个内容视图中计算你要的额外的宽高就。

<h4 id="win-previewFile">win.win.previewFile(path[, displayName]) <i>（MacOS）</i></h4>

 * `path` String类型 - 要使用QuickLook查看的文件的绝对路径。对于Quick Look这是非常重要的，因为要使用路径中的文件名和扩展名来决定打开文件内容的类型。
 * `displayName` String类型（可选参数）- 在Quick Look模态视图上展示的文件的名字。只是用来展示而不会决定文件的打开方式。默认是`path`中的。

使用[Quick Look](https://en.wikipedia.org/wiki/Quick_Look) 查看给定路径的文件。

<h4 id="win-closeFilePreview">win.closeFilePreview() <i>（MacOS）</i></h4>

关闭当前打开的[Quick Look](https://en.wikipedia.org/wiki/Quick_Look)面板

<h4 id="win-setBounds">win.setBounds(bounds[, animate])</h4>

 * `bounds` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型
 * `animate` Boolean类型（可选参数）MacOS有效

调整大小和移动窗口的界限。

<h4 id="win-getBounds">win.getBounds()</h4>

返回值为[Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型。

<h4 id="win-setContentBounds">win.setContentBounds(bounds[, animate])</h4>

 * `bounds` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型
 * `animate` Boolean类型（可选参数）MacOS有效

调整大小和移动窗口的客户端区域（例如网页）的范围。

<h4 id="win-getContentBounds">win.getContentBounds()</h4>

返回值为[Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型。

<h4 id="win-setSize">win.setSize(width, height[, animate])</h4>

 * `width` Integer类型
 * `height` Integer类型
 * `animate` Boolean类型（可选参数）MacOS有效

调整窗口大小的`width`和`height`。

<h4 id="win-getSize">win.getSize()</h4>

返回值为`Integer[]`类型 - 包含窗口的宽度和高度。

<h4 id="win-setContentSize">win.setContentSize(width, height[, animate]</h4>

 * `width` Integer类型
 * `height` Integer类型
 * `animate` Boolean类型（可选参数）MacOS有效

调整窗口客户端区域大小的`width`和`height`。

<h4 id="win-getContentSize">win.getContentSize()</h4>

返回值为`Integer[]`类型 - 包含窗口客户端区域的宽度和高度。

<h4 id="win-setMinimumSize">win.setMinimumSize(width, height)</h4>

 * `width` Integer类型
 * `height` Integer类型

设置窗口的`width`和`height`的最小尺寸。

<h4 id="win-getMinimumSize">win.getMinimumSize()</h4>

返回值为`Integer[]`类型 - 包含窗口的宽高的最小尺寸。

<h4 id="win-setMaximumSize">win.setMaximumSize(width, height)</h4>

 * `width` Integer类型
 * `height` Integer类型

设置窗口的`width`和`height`的最大尺寸。

<h4 id="win-getMaximumSize">win.getMaximumSize()</h4>

返回值为`Integer[]`类型 - 包含窗口的宽高的最大尺寸。

<h4 id="win-setResizable">win.setResizable(resizable)</h4>

 * `resizable` Boolean类型

设置窗口是否允许用户手动调整大小。

<h4 id="win-isResizable">win.isResizable()</h4>

返回值为`Boolean`类型 - 是否允许用户手动调整窗口的大小。

<h4 id="win-setMovable">win.setMovable(movable) <i>（MacOS，Windows）</i></h4>

 * `movable` Boolean类型

设置是否允许用户移动窗口。Linux下是无效的。

<h4 id="win-isMovable">win.isMovable() <i>（MacOS，Windows）</i></h4>

返回值为`Boolean`类型 - 是否允许用户移动窗口。

Linux下是总会返回`true`。

<h4 id="win-setMinimizable">win.setMinimizable(minimizable) <i>（MacOS，Windows）</i></h4>

 * `minimizable` Boolean类型

设置是否允许用户手动最小化窗口。Linux下是无效的。

<h4 id="win-isMinimizable">win.isMinimizable() <i>（MacOS，Windows）</i></h4>

返回值为`Boolean`类型 - 是否允许用户手动最小化窗口。

Linux下是总会返回`true`。

<h4 id="win-setMaximizable">win.setMaximizable(maximizable) <i>（MacOS，Windows）</i></h4>

 * `maximizable` Boolean类型

设置是否允许用户手动最大化窗口。Linux下是无效的。

<h4 id="win-isMaximizable">win.isMaximizable() <i>（MacOS，Windows）</i></h4>

返回值为`Boolean`类型 - 是否允许用户手动最大化窗口。

Linux下是总会返回`true`。

<h4 id="win-setFullScreenable">win.setFullScreenable(fullscreenable)</h4>

 * `fullscreenable` Boolean类型

设置是否允许最大化／缩放窗口按钮切换全屏模式或最大化窗口。

<h4 id="win-isFullScreenable">win.isFullScreenable()</h4>

返回值为Boolean类型 - 是否允许最大化／缩放窗口按钮切换全屏模式或最大化窗口。

<h4 id="win-setClosable">win.setClosable(closable) <i>（MacOS，Windows）</i></h4>

 * `closable` Boolean类型

设置是否允许用户手动关闭窗口。Linux下是无效的。

<h4 id="win-isClosable">win.isClosable() <i>（MacOS，Windows）</i></h4>

返回值为`Boolean`类型 - 是否允许用户手动关闭窗口。

Linux下是总会返回`true`。

<h4 id="win-setAlwaysOnTop">win.setAlwaysOnTop(flag[, level][, relativeLevel])</h4>

 * `flag` Boolean类型
 * `level` String类型（可选参数）MacOS有效 - 值包括了`normal`，`floating`，`torn-off-menu`，`modal-panel`，`main-menu`，`status`，`pop-up-menu`，`screen-saver`和 ~~`dock`~~（不推荐使用）。默认是`floating`。查看[MacOS文档](https://developer.apple.com/reference/appkit/nswindow/1664726-window_levels)获取更详细信息。
 * `relativeLevel` Integer类型（可选参数）MacOS有效 - 通过传入的`level`设置这个窗口的更高层数。默认是`0`。请注意，苹果禁止设置的层级高于1在屏幕保护程序之上的。

设置窗口是否需要总是显示在其他窗口的上面。设置之后，窗口仍然是一个正常的窗口，而不是一个不能被聚焦的工具窗口。

<h4 id="win-isAlwaysOnTop">win.isAlwaysOnTop()</h4>

返回值为`Boolean`类型 - 窗口是否总是在其他窗口的上面。

<h4 id="win-center">win.center()</h4>

移动窗口到屏幕中间。

<h4 id="win-setPosition">win.setPosition(x, y[, animate])</h4>

 * `x` Integer类型
 * `y` Integer类型
 * `animate` Boolean类型（可选参数）MacOS可用

移动窗口到`x`和`y`的位置。

<h4 id="win-getPosition">win.getPosition()</h4>

返回值为`Integer[]`类型 - 包含窗口的当前位置。

<h4 id="win-setTitle">win.setTitle(title)</h4>

 * `title` String类型

 使用`title`改变原生窗口的标题。

<h4 id="win-getTitle">win.getTitle()</h4>

返回值为`String`类型 - 原生窗口的标题。

**注意：**网页的标题可能和原生窗口的标题不同。

<h4 id="win-setSheetOffset">win.setSheetOffset(offsetY[, offsetX]) <i>（MacOS）</i></h4>

 * `offsetY` Float类型
 * `offsetX` Float类型（可选参数）

MacOS上改变表单的依附点。默认情况下，表单只依附在窗口边框下，但是你可能想要在一个HTML渲染工具栏下展示它们。例如：

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow()

    let toolbarRect = document.getElementById('toolbar').getBoundingClientRect()
    win.setSheetOffset(toolbarRect.height)

<h4 id="win-flashFrame">win.flashFrame(flag)</h4>

 * `flag` Boolean类型

开始或停止闪动窗口来吸引用户注意力。

<h4 id="win-setSkipTaskbar">win.setSkipTaskbar(skip)</h4>

 * `skip` Boolean类型

使窗口不在任务栏中显示。

<h4 id="win-setKiosk">win.setKiosk(flag)</h4>

 * `flag` Boolean类型

进入或者离开kiosk模式。

<h4 id="win-isKiosk">win.isKiosk()</h4>

返回值为`Boolean`类型 - 窗口是否在kiosk模式下。

<h4 id="win-getNativeWindowHandle">win.getNativeWindowHandle()</h4>

返回值为`Buffer`类型 - The platform-specific handle of the window.

原生操作类型在Windows下是`HWND`，在MacOS下是`NSView*`，在Linux下是`Window`（`unsigned long`）。

<h4 id="win-hookWindowMessage">win.hookWindowMessage(message, callback) <i>（Windows）</i></h4>

 * `message` Integer类型
 * `callback` Function类型

联播一个窗口信息。当消息在WndProc被收到时会调用`callback`。

<h4 id="win-isWindowMessageHooked">win.isWindowMessageHooked(message) <i>（Windows）</i></h4>

 * `message` Integer类型

返回值为`Boolean`类型 - `true`还是`false`取决于消息是否被联播。

<h4 id="win-unhookWindowMessage">win.unhookWindowMessage(message) <i>（Windows）</i></h4>

 * `message` Integer类型

取消联播窗口的消息。

<h4 id="win-unhookAllWindowMessages">win.unhookAllWindowMessages() <i>（Windows）</i></h4>

取消联播窗口的所有消息。

<h4 id="win-setRepresentedFilename">win.setRepresentedFilename(filename) <i>（macOS）</i></h4>

 * `filename` String类型

设置代表窗口的文件的路径名以及文件的图标，文件将会被展示在窗口的标题栏。

<h4 id="win-getRepresentedFilename">win.getRepresentedFilename() <i>（macOS）</i></h4>

返回值为`String` - 代表窗口的文件的路径名。

<h4 id="win-setDocumentEdited">win.setDocumentEdited(edited) <i>（macOS）</i></h4>

 * `edited` Boolean

指定窗口的文档是否已经被编辑，如果设置为`true`那么标题栏上的图标将会变成灰色。

<h4 id="win-isDocumentEdited">win.isDocumentEdited() <i>（macOS）</i></h4>

返回值为`Boolean`类型 - 窗口的文件是否被编辑。

<h4 id="win-focusOnWebView">win.focusOnWebView()</h4>
<h4 id="win-blurWebView">win.blurWebView()</h4>
<h4 id="win-capturePage">win.capturePage([rect, ]callback)</h4>

 * `rect` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md)类型（可选参数）- 捕捉到的边界。
 * `callback` Function类型
     * `image` [NativeImage](../both/nativeImage.md)

同`webContents.capturePage([rect, ]callback)`一样。

<h4 id="win-loadURL">win.loadURL(url[, options])</h4>

 * `url` String类型
 * `options` Object类型（可选参数）
     * `httpReferrer` String类型（可选参数）- 一个HTTP链接的地址。
     * `userAgent` String类型（可选参数）- 一个发起请求的用户代理。
     * `extraHeaders` String类型（可选参数）- 使用“\n”分隔的额外的头部。
     * `postData` ([UploadRawData](https://github.com/electron/electron/blob/master/docs/api/structures/upload-raw-data.md) | [UploadFile](https://github.com/electron/electron/blob/master/docs/api/structures/upload-file.md) | [UploadFileSystem](https://github.com/electron/electron/blob/master/docs/api/structures/upload-file-system.md) | [UploadBlob](https://github.com/electron/electron/blob/master/docs/api/structures/upload-blob.md))[]类型 - （可选参数）

同`webContents.loadURL(url[, options])`一样。

`url`可以是一个远程地址（例如`http://`）也可以是一个使用`file://`协议的本地的HTML文件路径。

请确保文件地址是正确的，推荐使用Node的`url.format`方法：

    let url = require('url').format({
        protocol: 'file',
        slashes: true,
        pathname: require('path').join(__dirname, 'index.html')
    })

    win.loadURL(url)

你可以像下面那样使用一个URL编码方式的`POST`请求加载一个URL：

    win.loadURL('http://localhost:8000/post', {
        postData: [{
            type: 'rawData',
            bytes: Buffer.from('hello=world')
        }],
        extraHeaders: 'Content-Type: application/x-www-form-urlencoded'
    })

<h4 id="win-reload">win.reload()</h4>

同`webContents.reload`一样。

<h4 id="win-setMenu">win.setMenu(menu) <i>（Linux，Windows）</i></h4>

 * `menu` Menu类型

设置`menu`作为窗口的菜单栏，设置它为`null`则将会移除菜单栏。

<h4 id="win-setProgressBar">win.setProgressBar(progress[, options])</h4>

 * `progress` Double类型
 * `options` Object类型（可选参数）
     * `mode` String类型 Windows有效 - 进度条的模式。可以是`none`，`normal`，`indeterminate`，`error`或者`paused`。

为进度条设置进度值。值的范围是[0-1.0]。

当进度 < 0则移除进度条；当进度 > 1时变为不确定模式。

Linux平台中，只支持Unity桌面环境，你需要在`package.json`文件中的`desktopName`中指定`*.desktop`文件名字。默认情况下，它假设为`app.getName().desktop`的值。

Windows中，一个模式可以被忽略。接受的值为`none`，`normal`，`indeterminate`，`error`和`paused`。如果你没有设置一个模式（没有一个值在有效范围内）就调用`setProgressBar`，值将会假设为`normal`。

<h4 id="win-setOverlayIcon">win.setOverlayIcon(overlay, description) <i>（Windows）</i></h4>

 * `overlay` [NativeImage](../both/nativeImage.md)类型 - 展示在任务栏右下角按钮上的图标。如果这个参数是`null`，则覆盖物将会被清除。
 * `description` String类型 - 一个将提供给给屏幕阅读器的描述。

设置一个16 x 16像素的覆盖物到当前任务栏图标上，通常被用来传达某种应用状态或者被动的通知用户。

<h4 id="win-setHasShadow">win.setHasShadow(hasShadow) <i>（MacOS）</i></h4>

 * `hasShadow` Boolean类型

设置窗口是否因该有一个阴影。Windows和Linux下无效。

<h4 id="win-hasShadow">win.hasShadow() <i>（MacOS）</i></h4>

返回值为`Boolean`类型 - 窗口是否有一个阴影。

Windows和Linux下总是返回`true`。

<h4 id="win-setThumbarButtons">win.setThumbarButtons(buttons) <i>（Windows）</i></h4>

 * `buttons` [ThumbarButton[]](https://github.com/electron/electron/blob/master/docs/api/structures/thumbar-button.md)类型

返回值为`Boolean`类型 - 按钮是否被成功的添加。

添加一个带有一些按钮的缩略图工具栏在一个任务栏按钮布局上的窗口的缩略图上。返回一个`Boolean`对象来表示这个小东西是否被添加成功。

缩略图工具栏上由于有限的空间按钮的数量应该不能超过7个。一旦你设置了缩略图工具栏，由于平台的限制，这个工具栏将不可以被移除。但是你可以调用这个接口传入一个空的数组来清空这些按钮。

`buttons`是一个`Button`对象的数组：

 * `Button` Object类型
     * `icon` NativeImage类型 - 在缩略图工具栏上展示的图标。
     * `click` Function类型
     * `tooltip` String类型（可选参数）- 这按钮的工具提示的文字。
     * `flags` String类型 - 控制按钮的特殊的状态和行为。通常来说这个值是`['enabled']`。

`flags`是一个可以包含下面字符串的数组：

 * `enabled` - 按钮是可点击的并且对用户有效。
 * `disabled` - 按钮是禁用的。按钮存在，但是有一个视觉状态表明它不可以响应用户的点击。
 * `dismissonclick` - 当按钮被点击，这个缩略图窗口将会被立刻关闭。
 * `nobackground` - 不绘制按钮的边框，只使用图片。
 * `hidden` - 按钮不展示给用户。
 * `noninteractive` - 按钮已经启用但是不进行交互；按下按钮的状态不会被绘制。这个值适用于被用在通知中的按钮中。

<h4 id="win-setThumbnailClip">win.setThumbnailClip(region) <i>（Windows）</i></h4>

 * `region` [Rectangle](https://github.com/electron/electron/blob/master/docs/api/structures/rectangle.md) - 窗口的区域。

设置当鼠标悬停在任务栏上时，窗口中展示缩略图的区域。你可以通过指定一个空的区域`{x: 0, y: 0, width: 0, height: 0}`来重新设置整个窗口的缩略图。

<h4 id="win-setThumbnailToolTip">win.setThumbnailToolTip(toolTip) <i>Windows</i></h4>

 * `toolTip` String类型

设置当鼠标悬停在任务栏的缩略图窗口上时展示的提示文字。

<h4 id="win-setAppDetails">win.setAppDetails(options) <i>（Windows）<i></h4>

 * `options` Object类型
     * `appId` String类型（可选参数）- 窗口的[App User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391569(v=vs.85).aspx)。它必须被设置，否则其他的设置选项将没有效果。
     * `appIconPath` String类型（可选参数）- 窗口的[Relaunch Icon](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391573(v=vs.85).aspx)。
     * `appIconIndex` Integer类型（可选参数） - `appIconPath`中的图标的索引值。当`appIconPath`没有被设置的时候将会被忽略。默认是`0`。
     * `relaunchCommand` String类型（可选参数）- 窗口的[Relaunch Command](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391571(v=vs.85).aspx)。
     * `relaunchDisplayName` String类型（可选参数）- 窗口的[Relaunch Display Name](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391572(v=vs.85).aspx)。

设置窗口的任务栏按钮的特性。

**注意：**`relaunchCommand`和`relaunchDisplayName`必须被一起设置。如果有一个没有被设置，那么它们都不会被使用。

<h4 id="win-showDefinitionForSelection">win.showDefinitionForSelection() <i>（MacOS）</i></h4>

同`webContents.showDefinitionForSelection()`一样。

<h4 id="win-setIcon">win.setIcon(icon) <i>（Windows，Linux）</i></h4>

 * `icon` [NativeImage](../both/nativeImage.md)

改变窗口的图标。

<h4 id="win-setAutoHideMenuBar">win.setAutoHideMenuBar(hide)</h4>

 * `hide` Boolean类型

设置窗口的菜单栏是否自动隐藏。一旦设置，菜单栏将只会在用户按下`Alt`键时展示。

如果菜单栏已经是可见的，调用`setAutoHideMenuBar(true)`将不会被立刻隐藏。

<h4 id="win-isMenuBarVisible">win.isMenuBarVisible()</h4>

返回值为`Boolean`类型 - 菜单栏是否是可见的。

<h4 id="win-setVisibleOnAllWorkspaces">win.setVisibleOnAllWorkspaces(visible)</h4>

 * `visible` Boolean类型

设置窗口在所有工作空间是否可见。

**注意：**这个接口将不会在Windows下工作。

<h4 id="win-isVisibleOnAllWorkspaces">win.isVisibleOnAllWorkspaces()</h4>

返回值为`Boolean`类型 - 窗口是否在所有工作空间是否可见。

**注意：**这个接口将不会在Windows下工作。

<h4 id="win-setIgnoreMouseEvents">win.setIgnoreMouseEvents(ignore)</h4>

 * `ignore` Boolean类型

使窗口忽略所有鼠标事件。

所有在这个窗口中发生的鼠标事件都会被转移到这个窗口的下面窗口中，但是如果这个窗口获得焦点，它仍然会接收键盘事件。

<h4 id="win-setContentProtection">win.setContentProtection(enable) <i>（MacOS，Windows）</i></h4>

 * `enable` Boolean类型

防止窗口的内容被其他的应用捕获。

MacOS中它会设置`NSWindow`的`sharingType`为`NSWindowSharingNone`。Windows中它会调用`SetWindowDisplayAffinity`传入`WDA_MONITOR`。

<h4 id="win-setFocusable">win.setFocusable(focusable) <i>（Windows）</i></h4>

 * `focusable` Boolean类型

改变窗口是否可以被聚焦。

<h4 id="win-setParentWindow">win.setParentWindow(parent) <i>（Linux，MacOS）</i></h4>

 * `parent` BrowserWindow类型

设置`parent`为当前窗口的父窗口，设置`null`将会使当前窗口放到一个顶级窗口中。

<h4 id="win-getParentWindow">win.getParentWindow()</h4>

返回值为`BrowserWindow`类型 - 父窗口。

<h4 id="win-getChildWindows">win.getChildWindows()</h4>

返回值为`BrowserWindow[]`类型 - 所有子窗口。

<h4 id="win-setAutoHideCursor">win.setAutoHideCursor(autoHide) <i>（MacOS）</i></h4>

 * `autoHide` Boolean类型

控制是否在输入时隐藏光标。

<h4 id="win-setVibrancy">win.setVibrancy(type) <i>（MacOS）</i></h4>

 * `type` String类型 - 可以是`appearance-based`，`light`，`dark`，`titlebar`，`selection`，`menu`，`popover`，`sidebar`，`medium-light`或`ultra-dark`。查看[macOS documentation](https://developer.apple.com/reference/appkit/nsvisualeffectview?language=objc)获取更多内容。

给浏览器窗口添加一个震动效果。传入`null`或者是空字符串将会移除窗口的震动效果。

<h4 id="win-setTouchBar">win.setTouchBar(touchBar) <i>（MacOS）</i></h4>

 * `touchBar` TouchBar类型

为当前窗口设置touchBar布局。指定`null`或者`undefined`将清除touch bar。这个方法只会在电脑有一个touch bar并且运行MacOS 10.12.1+系统上才会有效果。
