# 11.powerMonitor

> 原文：https://github.com/electron/electron/blob/master/docs/api/power-monitor.md    
译者：[Lin](https://github.com/ShmilyLin)   

> 监测电量状态改变

进程：[主进程](../../guides/glossary-of-terms.md#main-process)

你不能引用或使用这个模块，直到`ready`事件被`app`模块分发。

例如：

    const electron = require('electron')
    const {app} = electron

    app.on('ready', () => {
        electron.powerMonitor.on('suspend', () => {
            console.log('The system is going to sleep')
        })
    })

<h2 id="events">事件</h2>

`powerMonitor`模块会分发下面的事件：

<h3 id="event-">事件：'suspend'</h3>

当系统暂停（进入休眠状态）的时候被分发。

<h3 id="event-">事件：'resume'</h3>

当系统恢复（从休眠状态中结束）时被分发。

<h3 id="event-">事件：'on-ac' <i>（Windows）</i></h3>

当系统变为使用交流电充电时被分发。

<h3 id="event-">事件：'on-battery' <i>（Windows）</i></h3>

当系统变为使用电池电量时被分发。