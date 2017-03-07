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

<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>
<h4 id="event-">事件：</h4>

<h3 id="instance-methods">实例方法</h3>

<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>

<h3 id="instance-properties">实例属性</h3>

<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>
<h4 id="contents-"><code>contents.</code></h4>