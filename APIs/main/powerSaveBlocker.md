# 12.powerSaveBlocker

> 原文：https://github.com/electron/electron/blob/master/docs/api/power-save-blocker.md    
译者：[Lin](https://github.com/ShmilyLin)   

> 阻止系统进入低功耗（休眠）模式

进程：[主进程](../../guides/glossary-of-terms.md#main-process)

例如：

    const {powerSaveBlocker} = require('electron')

    const id = powerSaveBlocker.start('prevent-display-sleep')
    console.log(powerSaveBlocker.isStarted(id))

    powerSaveBlocker.stop(id)

<h2 id="methods">Methods</h2>

`powerSaveBlocker`模块有下面的方法：

<h3 id="powerSaveBlocker-start"><code>powerSaveBlocker.start(type)</code></h3>

 * `type` String类型 - 电量保持拦截器类型。
     * `prevent-app-suspension` - 防止系统暂停。保持系统活动，但是允许关闭屏幕。例如在这种情况下使用：下载一个文件或者播放音频。
     * `prevent-display-sleep` - 防止显示器进入睡眠状态。保持系统和屏幕活动。例如在这种情况下使用：播放视频。

返回值为`Integer`类型 - 分配给这个电量拦截器的拦截器ID。

启动防止系统进入低电量模式。返回一个`integer`类型的电量保持拦截器标识。

**注意：**`prevent-display-sleep`比`prevent-app-suspension`有更高的优先级。只有最高优先级的类型会生效。换句话说，`prevent-display-sleep`总是优先于`prevent-app-suspension`。

例如，一个接口A调用一个`prevent-app-suspension`请求，并且其他接口B调用`prevent-display-sleep`请求。`prevent-display-sleep`将会被使用直到B停止它的请求。然后，`prevent-app-suspension`才会被使用。

<h3 id="powerSaveBlocker-stop"><code>powerSaveBlocker.stop(id)</code></h3>

 * `id` Integer类型 - `powerSaveBlocker.start`返回的电量保持拦截器ID。

停止设置电量保持拦截器。

<h3 id="powerSaveBlocker-isStarted"><code>powerSaveBlocker.isStarted(id)</code></h3>

 * `id` Integer类型 - `powerSaveBlocker.start`返回的电量保持拦截器ID。

返回值为`Boolean`类型 - 对应的`powerSaveBlocker`是否被启动。