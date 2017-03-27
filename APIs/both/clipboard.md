# 1.clipboard

> 原文：https://github.com/electron/electron/blob/master/docs/api/clipboard.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 使用系统剪贴板执行复制和粘贴操作。

进程：[主进程](../../guides/glossary-of-terms.md#main-process)，[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

下面的示例展示了如何将一个字符串写入剪贴板：

```javascript
const {clipboard} = require('electron')
clipboard.writeText('Example String')
```

On X Window systems, there is also a selection clipboard. To manipulate it you need to pass `selection` to each method:

```javascript
const {clipboard} = require('electron')
clipboard.writeText('Example String', 'selection')
console.log(clipboard.readText('selection'))
```

## 方法

`clipboard`模块有以下方法：

**注意：**实验性的接口会被标记为`Experimental`，有可能在未来的版本中移除。

### `clipboard.readText([type])`

 * `type` String类型（可选参数）

返回值为`String`类型 - 剪贴板中的纯文本格式的内容。

### `clipboard.writeText(text[, type])`

 * `text` String类型
 * `type` String类型（可选参数）

以纯文本格式将文本写入到剪贴板中。

### `clipboard.readHTML([type])`

 * `type` String类型（可选参数）

返回值为`String`类型 - 剪贴板中的标记语言格式的内容。

### `clipboard.writeHTML(markup[, type])`

 * `markup` String类型
 * `type` String类型（可选参数）

以标记语言格式将文本写入到剪贴板中。

### `clipboard.readImage([type])`

 * `type` String类型（可选参数）

返回值为[`NativeImage`](./nativeImage.md)类型 - 剪贴板中的图像内容。

### `clipboard.writeImage(image[, type])`

 * `image` [`NativeImage`](./nativeImage.md)
 * `type` String类型（可选参数）

讲图像写入剪贴板。

### `clipboard.readRTF([type])`

 * `type` String类型（可选参数）

返回值为`String`类型 - 剪贴板中的RTF格式的内容。

### `clipboard.writeRTF(text[, type])`

 * `text` String类型
 * `type` String类型（可选参数）

将文本以RTF格式写入到剪贴板中。

### `clipboard.readBookmark()` *MacOS Windows*

返回值为`Object`类型：

 * `title` String类型
 * `url` String类型

返回一个包含`title`和`url`键表示的书签的对象。如果书签无法获取，那么`title`和`url`的值为空的字符串。

### `clipboard.writeBookmark(title, url[, type])` *MacOS Windows*

 * `title` String类型
 * `url` String类型
 * `type` String类型（可选参数）

将一个`title`和`url`表示的书签写入到剪贴板中。

**注意：**大多数Windows应用中不支持粘贴书签到应用中，所以你可以使用`clipboard.write`来同时写入一个书签和一个备用文本到剪贴板。

```javascript
clipboard.write({
    text: 'https://electron.atom.io',
    bookmark: 'Electron Homepage'
})
```

### `clipboard.readFindText()` *MacOS*

返回值为`String`类型 - find pasteboard中的文本。当在渲染线程中调用这个方法的时候将会使用同步进程通讯。每当应用被激活都会从将find pasteboard中的值存入缓存。

### `clipboard.writeFindText(text)` *MacOS*

 * `text` String类型

将文本以纯文本格式写入到find pasteboard中。当在渲染线程中调用这个方法的时候将会使用同步进程通讯。

### `clipboard.clear([type])`

 * `type` String类型（可选参数）

清空剪贴板的内容。

### `clipboard.availableFormats([type])`

 * `type` String类型（可选参数）

返回值为`String[]`类型 - 剪贴板`type`支持的类型的组成的数组。

### `clipboard.has(format[, type])` *Experimental*

 * `format` String类型
 * `type` String类型（可选参数）

返回值为`Boolean`类型 - 剪贴板是否支持指定的`format`。

```javascript
const {clipboard} = require('electron')
console.log(clipboard.has('<p>selection</p>'))
```

### `clipboard.read(format)` *Experimental*

 * `format` String类型

返回值为`String`类型 - 从剪贴板中读取`format`类型。

### `clipboard.readBuffer(format)` *Experimental*

 * `format` String类型

返回值为`Buffer`类型 - 从剪贴板中读取`format`类型。

### `clipboard.write(data[, type])`

 * `data` Object类型
     * `text` String类型（可选参数）
     * `html` String类型（可选参数）
     * `image` NativeImage类型（可选参数）
     * `rtf` String类型（可选参数）
     * `bookmark` String类型（可选参数）- `text`中的地址对应的标题。
 * `type` String类型（可选参数）
