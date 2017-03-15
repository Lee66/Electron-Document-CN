# 5.dialog

> 原文：https://github.com/electron/electron/blob/master/docs/api/dialog.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 显示本地系统的对话框来打开或者保存文件、提示等等。

进程：[主进程](../../guides/glossary-of-terms.md#main-process)   

一个展示对话框来选择多个文件和目录的例子：

    const {dialog} = require('electron')
    console.log(dialog.showOpenDialog({properties: ['openFile', 'openDirectory', 'multiSelections']}))

对话框从Electron的主进程中被打开。如果你想在渲染进程中使用dialog对象，记得使用`remote`来获取它：

    const {dialog} = require('electron').remote
    console.log(dialog)

## 方法

`dialog`模块有以下方法：

### `dialog.showOpenDialog([browserWindow, ]options[, callback])`

 * `browserWindow` BrowserWindow类型（可选参数）
 * `options` Object类型
     * `title` String类型（可选参数）
     * `defaultPath` String类型（可选参数）
     * `buttonLabel` String类型（可选参数）- 自定义确认按钮的标签，当留空时将会使用默认标签。
     * `filters` [FileFilter[]](https://github.com/electron/electron/blob/master/docs/api/structures/file-filter.md)类型（可选参数）
     * `properties` String类型 - 包含将被对话框使用的功能。支持使用下面的值：
         * `openFile` - 允许选择文件。
         * `openDirectory` - 允许选择目录。
         * `multiSelections` - 允许选择多个路径。
         * `showHiddenFiles` - 在对话框中展示隐藏文件。
         * `createDirectory` - 在对话框中允许创建文件夹。*MacOS*
         * `promptToCreate` - 如果在对话框中进入了一个不存在的文件路径将会提示创建这个文件路径。这个实际上并不会在这个路径下创建文件，而是将返回这个不存在的路径，然后使用应用来创建。*Windows*
         * `noResolveAliases` - 不允许自动解析别名（符号解析）路径。选定的别名将返回别名路径而不是他们的目标路径。*MacOS*
     * `normalizeAccessKeys` Boolean类型（可选参数）- 跨平台的标准化键盘。默认为`false`。允许设定放置在按钮标签上的键盘快捷键`&`，并且为了使它们在每个平台上都正常工作它们将被替换，`&`字符将在MacOS上被移除，在Linux上被替换为`_`，在Windows上则保持不变。例如，一个`Vie&w`按钮标签将在被替换为`Vie_w`，在MacOS上是`View`，在Windows喝Linux上通过`Alt-w`被选中。
         * `message` String类型（可选参数）*MacOS* - 显示在输入框中的信息。
 * `callback` Function类型（可选参数）
     * `filePaths` String[]类型 - 用户选中的文件路径的数组。

返回值为`String[]`类型，一个被用户选中的文件路径的数组，如果`callback`被启用那么就返回`undefined`。

`browserWindow`参数允许对话框把它自己固定到一个父窗口上，使他自己变为模态状态。

当你想对用户限制指定的类型时，`filters`指定一个可以被显示或选择的文件类型的数组。例如：

    {
        filters: [
            {name: 'Images', extensions: ['jpg', 'png', 'gif']},
            {name: 'Movies', extensions: ['mkv', 'avi', 'mp4']},
            {name: 'Custom File Type', extensions: ['as']},
            {name: 'All Files', extensions: ['*']}
        ]
    }

`extensions`数组 array should contain extensions without wildcards or dots (e.g. `'png'` is good but `'.png'` and `'*.png'` are bad). To show all files, use the `'*'` wildcard (no other wildcard is supported).

If a `callback` is passed, the API call will be asynchronous and the result will be passed via `callback(filenames)`

**注意：**On Windows and Linux an open dialog can not be both a file selector and a directory selector, so if you set `properties` to `['openFile', 'openDirectory']` on these platforms, a directory selector will be shown.

<h3 id="dialog-"><code>dialog.</code></h3>
<h3 id="dialog-"><code>dialog.</code></h3>
<h3 id="dialog-"><code>dialog.</code></h3>
