# 1.术语表

> 原文：https://github.com/electron/electron/blob/master/docs/glossary.md    
译者：[Lin](https://github.com/ShmilyLin)   

这个页面定义了一般在Electron中使用的专有名词。

<h3 id="ASAR">ASAR</h3>

ASAR是Atom Shell Archive Format的简称。一个[asar](https://github.com/electron/asar)文档是一个把文件都放在一个单独的文件中的简单的tar-like类型文件。Electron可以从中读取任意的文件而不用解压整个文件。

创造ASAR类型主要是为了在Windows下提高性能... TODO

<h3 id="Brightray">Brightray</h3>

[Brightray](https://github.com/electron/brightray)是一个使[libchromiumcontent](#libchromiumcontent)更容易使用应用的静态库。它是专门为了Electron而创造的，但是也可以允许没有基于Electron的原生应用使用Chromium的渲染引擎。   

Brightray是Electron的一个底层的依赖，大多数Electron的使用者并不会关心它。

<h3 id="DMG">DMG</h3>

Apple Disk Image是一个在MacOS上使用的打包类型。DMG文件通常用来分发应用的“安装文件”。[electron-builder](https://github.com/electron-userland/electron-builder)支持dmg作为一个打包目标。

<h3 id="IPC">IPC</h3>

IPC是Inter-Process Communication的简称。Electron使用IPC在[主进程](#main-process)和[渲染进程](#renderer-process)之间发送联播JSON消息。

<h3 id="libchromiumcontent">libchromiumcontent</h3>

一个包含了Chromium内容模块和所有依赖（例如，Blink，[V8](#v8)等）的简单的共享库。

<h3 id="main-process">main process</h3>

主进程，通常是一个叫做`main.js`的文件，是指向每一个Electron应用的入口。它控制着应用从打开到关闭的生命周期。它也管理着原生元素，比如`Menu`，`Menu Bar`，`Dock`，`Tray`等。主进程在应用中承担着创建每一个新的渲染进程的责任。全部的Node接口都在它里面。

每一个应用的主线程文件是在`package.json`文件中的`main`属性中被指定的。这是`electron .`如何知道启动时要执行哪个文件的原因。

参见：[process](#process)，[renderer process](#renderer-process)

<h3 id="MAS">MAS</h3>

Apple's Mac App Store的缩写。关于提交你的应用程序到MAS的详细信息，请看[Mac App Store Submission Guide](./mac-app-store-submission-guide.html)。

<h3 id="native-modules">native modules</h3>

Native modules（在Node.js中也叫[addons](https://nodejs.org/api/addons.html)）是C或C++写的模块，使用`require()`函数可以被加载到Node.js或Electron中，然后就可以像一个普通Node.js模块一样使用了。它们主要用来提供一个在Node.js上运行的JavaScript河C/C++库之间的接口。

Electron支持Native Node modules，但是由于Electron非常有可能使用安装在你电脑上的Node二进制文件中的不同版本的V8，你在编译native modules的时候需要手动指定Electron的头部位置。

参考[Using Native Node Modules](./using-native-node-modules.html)。

<h3 id="NSIS">NSIS</h3>

Nullsoft Scriptable Install System是一个Microsoft Windows下的脚本驱动的安装制作工具。它发布在免费软件许可证下，是一个类似于InstallShield的广泛的被用来替代商业专有产品的工具。[electron-builder](https://github.com/electron-userland/electron-builder)支持NSIS作为一个编译目标。

<h3 id="process">process</h3>

一个进程是一个正在运行的计算机程序的实例。Electron应用实际上是利用[主进程](#main-process)和一个或几个[渲染进程](#renderer-process)同时运行几个程序。

Node.js和Electron中，每一个运行着的进程都是一个`process`对象。这个对象是一个全局的并提供关于当前进程的信息和控制。作为一个全局的，它在应用中不使用require()也是有效的。

参见：[main process](#main-process), [renderer process](#renderer-process)

<h3 id="renderer-process">renderer process</h3>

在你的应用中，渲染进程就是一个浏览器窗口。不同于主进程，可以有多个渲染进程并且每一个渲染进程都作为一个分隔的进程来运行。它们也可以被隐藏。

一般的浏览器中，网页通常运行在一个沙盒环境中，并且不允许调用本地资源。Electron的使用者有权利使用Node.js接口来与底层的操作系统哦交互。

参考：[process](#process)，[main process](#main-process)

<h3 id="squirrel">Squirrel</h3>

Squirrel是一个开源的框架，可以允许Electron应用自动升级到已经发布的最新版本。查看[autoUpdater](../APIs/main/autoUpdater.html)接口的使用Squirrel启动的信息。

<h3 id="userland">userland</h3>

这个术语来自于Unix社区，"userland"或"userspace"在运行在操作系统内核之外的程序中被提及。最近，这个术语已经在Node和npm社区中普及，用于区分"Node core"和npm上记录的通过更大的"user"社区发布的包。

像Node，Electron是一个专注于有一个小的接口集合，并且这个集合提供所有的必须的为了开发多平台桌面程序的原生接口。这个设计理念使得Electron保持为一个灵活的工具，而不是过多的规定如何来使用它。Userland使得用户可以创建并分享工具，而这些工具提供基于“core”中有效内容之上的附加功能。

<h3 id="v8">V8</h3>

V8是Google的开源JavaScrip引擎。它是用C++编写的并且被用在Google Chrome中，Chrome是Google的开源浏览器。V8可以单独运行，或者被嵌入到任何C++应用中。

<h3 id="webview">webview</h3>

`webview`标签是被用来在你的Electron应用中嵌入“guest”（例如一个外部网页）内容。他们是非常相似的内嵌框架，但是不同之处在于每一个`webview`运行在一个指定的进程中。它并没有和你的网页拥有相同的权限，并且在你的应用和嵌入内容之间交互都是异步的。这将保持你的应用对于嵌入内容的安全性。
