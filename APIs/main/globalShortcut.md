<h1 id="globalShortcut">6. globalShortcut</h1>

> 原文：https://github.com/electron/electron/blob/master/docs/api/global-shortcut.md    
译者：[Lin](https://github.com/ShmilyLin)   

> 当程序没有键盘焦点时删除键盘事件。

线程：[主进程](../../guides/glossary-of-terms.md#main-process)

`globalShortcut`模块可以注册／注销一个操作系统的全局快捷键，所以你可以为操作自定义各种各样的快捷键。

**注意：**快捷键是全局的；甚至这个应用没有键盘焦点时也会工作。你不能使用这个模块，直到`app`模块的`ready`事件被分发。

    const {app, globalShortcut} = require('electron')

    app.on('ready', () => {
        // 注册一个'CommandOrControl+X'快捷键的监听。
        const ret = globalShortcut.register('CommandOrControl+X', () => {
            console.log('CommandOrControl+X被按下')
        })

        if (!ret) {
            console.log('注册失败')
        }

        // 检查这个快捷键是否被注册。
        console.log(globalShortcut.isRegistered('CommandOrControl+X'))
    })

    app.on('will-quit', () => {
        // 注销一个快捷键。
        globalShortcut.unregister('CommandOrControl+X')

        // 注销所有快捷键。
        globalShortcut.unregisterAll()
    })

<h2 id="methods">Methods</h2>

`globalShortcut`模块有下面的方法：

<h3 id="globalShortcut-register"><code>globalShortcut.register(accelerator, callback)</code></h3>

 * `accelerator` [Accelerator](https://github.com/electron/electron/blob/master/docs/api/accelerator.md)类型
 * `callback` Function类型

注册一个`accelerator`传入的全局的快捷键。`callback`被调用当注册的快捷键被用户按下的时候。

当`accelerator`已经被其他应用占用，这个调用将没有任何反应的失败。这个行为是操作系统控制，因为它们（指操作系统）不想让应用程序因为全局快捷键而产生冲突。

<h3 id="globalShortcut-isRegistered"><code>globalShortcut.isRegistered(accelerator)</code></h3>

 * `accelerator` [Accelerator](https://github.com/electron/electron/blob/master/docs/api/accelerator.md)类型

返回值为`Boolean`类型 - 应用是否已经注册`accelerator`。

当`accelerator`已经被其他应用程序占用，这个方法将返回`false`。这个行为是操作系统控制，因为它们（指操作系统）不想让应用程序因为全局快捷键而产生冲突。

<h3 id="globalShortcut-unregister"><code>globalShortcut.unregister(accelerator)</code></h3>

 * `accelerator` [Accelerator](https://github.com/electron/electron/blob/master/docs/api/accelerator.md)类型

传入`accelerator`来注销一个全局快捷键。

<h3 id="globalShortcut-unregisterAll"><code>globalShortcut.unregisterAll()</code></h3>

注销所有全局快捷键。