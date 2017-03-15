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

## 事件

`powerMonitor`模块会分发下面的事件: 

### 事件: 'suspend'

当系统暂停（进入休眠状态）的时候被分发。

### 事件: 'resume'

当系统恢复（从休眠状态中结束）时被分发。

### 事件: 'on-ac' *Windows*

当系统变为使用交流电充电时被分发。

### 事件: 'on-battery' *Windows*

当系统变为使用电池电量时被分发。