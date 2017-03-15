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

`extensions`数组应该包含没有通配符或点符号的扩展名（例如，`'png'`是正确的，`'.png'`和`'*.png'`是错误的）。要展示所有文件请使用`'*'`通配符（不支持其他的通配符）。

如果传入了一个`callback`，这个接口将会被异步调用，并将结果通过`callback(filenames)`传回。

**注意：**Windows和Linux下，一个打开的对话框不可能既是一个文件选择器也是一个文件夹选择器，所以如果你在这两个平台上设置`properties`为`['openFile', 'openDirectory']`，那么将展示一个文件夹选择器。

### `dialog.showSaveDialog([browserWindow, ]options[, callback])`

 * `browserWindow` BrowserWindow类型（可选参数）
 * `options` Object类型
     * `title` String类型（可选参数）
     * `defaultPath` String类型（可选参数）
     * `buttonLabel` String类型（可选参数）- 自定义确认按钮的标签，当留空时将使用默认标签。
     * `filters` FileFilter[]类型（可选参数）
     * `message` String类型（可选参数）*MacOS* - 显示的文本框上的信息。
     * `nameFieldLabel String (optional) *MacOS* - 自定义文件名文本框前面展示的文本。
     * `showsTagField` Boolean类型（可选参数）*MacOS* - 展示标记输入框，默认为`true`。
 * `callback` Function类型（可选参数）
     * `filename` String类型

返回值为`String`类型, 一个被用户选中的文件路径，如果`callback`被启用那么就返回`undefined`。

`browserWindow`参数允许对话框把它自己固定到一个父窗口上，使他自己变为模态状态。

`filters`指定一个可以被显示或选择的文件类型的数组。查看`dialog.showOpenDialog`的例子。

如果传入了一个`callback`，这个接口将会被异步调用，并将结果通过`callback(filename)`传回。

### `dialog.showMessageBox([browserWindow, ]options[, callback])`

 * `browserWindow` BrowserWindow类型（可选参数）
 * `options` Object类型
     * `type` String类型（可选参数）- 可以是`"none"`、`"info"`、`"error"`、`"question"`或`"warning"`。Windows中，`"question"`和`"info"`显示的图标是一样的，除非你使用`"icon"`选项设置一个图标。
     * `buttons` String类型 - 按钮的文本数组。Windows中，一个空的数组将会导致在按钮的标记内容是`"OK"`。
     * `defaultId` Integer类型（可选参数）- 当消息对话框打开的时候，在按钮数组中的默认被选中的按钮的索引。
     * `title` String类型（可选参数）- 消息对话框的标题，一些平台可能不会展示它。
     * `message` String - 消息对话框的内容。
     * `detail` String类型（可选参数）- 消息的额外信息。
     * `checkboxLabel` String类型（可选参数）- 如果设置了，消息对话框将包括一个给定标签的复选框。当使用`callback`的时候这个复选框的状态可以被检验。
     * `checkboxChecked` Boolean类型（可选参数）- 初始化复选框的选择状态。默认是`false`。
     * `icon` [NativeImage](../both/nativeImage.md)类型（可选参数）
     * `cancelId` Integer类型（可选参数）- 设置的这个按钮的索引将通过`Esc`键被用来取消对话框。默认情况下指定第一个按钮，并且使用`"cancel"`或者`"no"`作为这个按钮的标签。如果没有这样的标记的按钮存在并且这个选项也没有被设置，那么`0`将被作为函数返回值或者回调的返回值。这个选项在Windows下被忽略。
     * `noLink` Boolean类型（可选参数）- Windows中，Electron将尝试计算出`buttons`中的普通按钮（例如`Cancel`或`Yes`），并且将其他的按钮作为命令链接展示在对话框中。这可能导致对话框展示为现代Windows应用风格。如果你不喜欢这个行为，你可以设置`noLink`为`true`。
 * `callback` Function类型（可选参数）
     * `response` Number类型 - 被点击的按钮的索引。
     * `checkboxChecked` Boolean类型 - 如果设置了`checkboxLabel`，那么这个值是复选框的选择状态。否则为`false`。

返回值为`Integer`类型，是选中的按钮的索引值，如果提供了一个回调，那么它将返回`undefined`。

展示一个消息盒子，它将阻塞进程直到消息对话框被关闭。它返回被点击的按钮的索引值。

`browserWindow`参数允许对话框把它自己固定到一个父窗口上，使他自己变为模态状态。

如果传入了一个`callback`，这个接口将会被异步调用，并将结果通过`callback(response)`传回。

### `dialog.showErrorBox(title, content)`

 * `title` String类型 - 在错误对话框中展示的标题。
 * `content` String类型 - 在错误对话框中展示的文字内容。

显示一个模态对话框来展示一个错误信息

这个接口可以在`app`模块的`ready`事件被分发之前被安全的调用，它通常被用来在启动的早期报告错误。在Linux中如果载应用的`ready`事件之前被调用，这个消息将被分发为一个标准错误，将展示一个没有GUI的对话框。

## 表

MacOS下，如果你给`BrowserWindow`参数提供了一个`browserWindow`引用，那么对话框将以窗口的附属表的形式展示，如果没有窗口被提供那么就是模态状态。

你可以调用`BrowserWindow.getCurrentWindow().setSheetOffset(offset)`来改变相对于表附属的窗口的边框的位置。