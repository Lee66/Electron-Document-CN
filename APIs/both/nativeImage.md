# 3.nativeImage

> 原文：https://github.com/electron/electron/blob/master/docs/api/native-image.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 使用PNG或JPG文件创建托盘图标，dock图标和应用图标。

进程：[主进程](../../guides/glossary-of-terms.md#main-process)，[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

Electron中，对于需要图像的接口，你可以传入文件的路径或者`NativeImage`实例。当传入`null`时将使用一个空的图像。

例如，当创建一个托盘活着设置一个窗口的图标，你可以传入一个`String`类型的图像文件的地址：

```javascript
const {BrowserWindow, Tray} = require('electron')

const appIcon = new Tray('/Users/somebody/images/icon.png')
let win = new BrowserWindow({icon: '/Users/somebody/images/window.png'})
console.log(appIcon, win)
```

或者从剪贴板返回的`NativeImage`中读取图像：

```javascript
const {clipboard, Tray} = require('electron')
const image = clipboard.readImage()
const appIcon = new Tray(image)
console.log(appIcon)
```

## 支持的类型

当前支持`PNG`和`JPEG`图像类型。建议使用`PNG`，因为它支持透明和无损压缩。

Windows中，你也可以从文件路径中加载`ICO`图标。为了最好的可视化效果，建议至少包含以下尺寸：

 * Small icon 
     * 16x16 (100% DPI scale)
     * 20x20 (125% DPI scale)
     * 24x24 (150% DPI scale)
     * 32x32 (200% DPI scale)
 * Large icon 
     * 32x32 (100% DPI scale)
     * 40x40 (125% DPI scale)
     * 48x48 (150% DPI scale)
     * 64x64 (200% DPI scale)
 * 256x256

点击[this article](https://msdn.microsoft.com/en-us/library/windows/desktop/dn742485(v=vs.85).aspx)中的*Size requirements*部分。

## 高分辨率图像

有支持高DPI的平台上，例如苹果Retina显示屏，你可以在图像的基本文件名的后面添加`@2x`来标记这个事一个高分辨率的图像。

例如，如果`icon.png`是一个标准分辨率的正常图像，那么`icon@2x.png`将被处理为一个有两倍DPI密度的高分辨率图像。

如果你想同时支持不同DPI密度显示屏，你可以将不同尺寸的图像放在同一个文件夹下，并且使用不包含DPI后缀的文件名。例如：

    images/
    ├── icon.png
    ├── icon@2x.png
    └── icon@3x.png


```javascript
const {Tray} = require('electron')
let appIcon = new Tray('/Users/somebody/images/icon.png')
console.log(appIcon)
```

支持以下DPI的后缀名：

 * `@1x`
 * `@1.25x`
 * `@1.33x`
 * `@1.4x`
 * `@1.5x`
 * `@1.8x`
 * `@2x`
 * `@2.5x`
 * `@3x`
 * `@4x`
 * `@5x`

## 模版图像

模版图像包括黑色和无颜色（和一个透明通道）。模版图像是不打算被作为单独的图像而使用的，并且通常和其他内容混合创建一个理想的最终形态。

最常见的情况是在菜单栏图标上使用模版图像，所以它可以适应浅色和暗色的菜单栏。

**注意：**只有MacOS支持模版图像。

为了使用一个模版图像，它的文件名字必须以`Template`单词结尾。例如：

 * `xxxTemplate.png`
 * `xxxTemplate@2x.png`

## 方法

`nativeImage`模块又以下方法，所有的方法斗返回一个`NativeImage`类的实例：

### `nativeImage.createEmpty()`

返回值为`NativeImage`类型

创建一个空的`NativeImage`实例。

### `nativeImage.createFromPath(path)`

 * `path` String类型

返回值为`NativeImage`类型

使用本地文件地址创建一个新的`NativeImage`实例。如果`path`不存在，或者不能被读取，或者不是一个图像，那么这个方法返回一个空的图像。

```javascript
const nativeImage = require('electron').nativeImage

let image = nativeImage.createFromPath('/Users/somebody/images/icon.png')
console.log(image)
```

### `nativeImage.createFromBuffer(buffer[, options])`

 * `buffer` [Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)类型
 * `options` Object类型（可选参数）
     * `width` Integer类型（可选参数）- 对于bitmap buffers是必须的参数。
     * `height` Integer类型（可选参数）- 对于bitmap buffers是必须的参数。
     * `scaleFactor` Double类型（可选参数）- 默认是1.0。

返回值为`NativeImage`类型

通过`buffer`创建一个新的`NativeImage`实例。

### `nativeImage.createFromDataURL(dataURL)`

 * `dataURL` String类型

通过`dataURL`创建一个新的`NativeImage`实例。

## 类: NativeImage

> 使用PNG或JPG文件创建托盘图标，dock图标和应用图标。


进程：[主进程](../../guides/glossary-of-terms.md#main-process)，[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

### 实例的方法

`NativeImage`类的实例有以下方法：

#### `image.toPNG([options])`

 * `options` Object类型（可选参数）
     * `scaleFactor` Double类型（可选参数）- 默认是1.0。

返回值为`Buffer`类型 - 一个包含图像的`PNG`编码数据的[Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)。

#### `image.toJPEG(quality)`

 * `quality` Integer类型（**必传参数**）- 在0 - 100之间。

返回值为`Buffer`类型 - 一个包含图像的`JPEG`编码数据的[Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)。

#### `image.toBitmap([options])`

 * `options` Object类型（可选参数）
     * `scaleFactor` Double类型（可选参数）- 默认为1.0。

返回值为`Buffer`类型 - 一个包含图像的原始位图的数据副本的[Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)。

#### `image.toDataURL([options])`

 * `options` Object类型（可选参数）
     * `scaleFactor` Double类型（可选参数）- 默认为1.0。

返回值为`String`类型 - 图片的数据地址。

#### `image.getBitmap([options])`

 * `options` Object类型（可选参数）
     * `scaleFactor` Double类型（可选参数）- 默认值为1.0。

返回值为`Buffer`类型 - 一个包含图像的原始位图的数据的[Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)。

`getBitmap()`和`toBitmap()`的区别是，`getBitmap()`不会复制位图数据，所以你必须在当前事件循环标记中直接使用返回的Buffer，否则数据将会被改变或者被销毁。

#### `image.getNativeHandle()` *MacOS*

返回值为`Buffer`类型 - 一个存储底层处理本地图像的C指针的[Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer)。MacOS中，将返回一个指向`NSImage`实例的指针。

请注意，返回的指针是一个指向底层的本地土图像来代替一个副本的弱指针，所以你必须确保对应的`nativeImage`实例一直存在。

#### `image.isEmpty()`

返回值为`Boolean`类型 - 图像是否是空。

#### `image.getSize()`

返回值为`Object`类型：

 * `width` Integer类型
 * `height` Integer类型

#### `image.setTemplateImage(option)`

 * `option` Boolean类型

将图像转为一个模版图像。

#### `image.isTemplateImage()`

返回值为`Boolean`类型 - 图像是否是一个模版图像。

#### `image.crop(rect)`

 * `rect` Object类型 - 图像要修剪的区域
     * `x` Integer类型
     * `y` Integer类型
     * `width` Integer类型
     * `height` Integer类型

返回值为`NativeImage`类型 - 被修剪的图像。

#### `image.resize(options)`

 * `options` Object类型
     * `width` Integer类型（可选参数）- 默认是图像的宽
     * `height` Integer类型（可选参数）- 默认是图像的高
     * `quality` String类型（可选参数）- 期望的重设大小的图片的质量。值可以是`good`、`better`或`best`。默认是`best`。这个值表示一个期望的质量和速度。它们将转化为一个具体的算法，这个算法取决于底层平台的处理能力（CPU，GPU）。这三个方法可能在一个给定的平台上被映射到同一个算法上。

返回值为`NativeImage` - 重设大小的图像。

如果只指定了`height`或者`width`，那么将使用当前的纵横比来重设图像。

#### `image.getAspectRatio()`

返回值为`Float`类型 - 图像的纵横比。

#### `image.addRepresentation(options)`

 * `options` Object类型
     * `scaleFactor` Double类型 - The scale factor to add the image representation for.
     * `width` Integer类型（可选参数）- 默认为`0`。如果一个二进制位图传给了`buffer`，那么这个选项必须设置。
     * `height` Integer类型（可选参数）- 默认为`0`。如果一个二进制位图传给了`buffer`，那么这个选项必须设置。
     * `buffer` Buffer类型（可选参数）- 包含原始图像数据的buffer。
     * `dataURL` String类型（可选参数）- 包含了base64编码的PNG或JPEG图像的数据地址。

添加一个指定的缩放因数的图像表现。这个可以被用来明确的添加不同缩放因素表现到一个图像上。这个方法可以在空图像上调用。