# 4.webFrame

> 原文：https://github.com/electron/electron/blob/master/docs/api/web-frame.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 自定义当前网页的绘制

进程：[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

一个缩放当前页面到200%的例子。

```javascript
const {webFrame} = require('electron')

webFrame.setZoomFactor(2)
```

## 方法

`webFrame`模块有以下方法：

### `webFrame.`

 * `factor` Number类型 - 缩放因数。

改变缩放因数为指定的因数。缩放因数是缩放百分比除以100，所以300% = 3.0。

### `webFrame.getZoomFactor()`

返回值为`Number`类型 - 当前的缩放因数。

### `webFrame.setZoomLevel(level)`

 * `level` Number类型 - 缩放等级。

改变缩放等级为指定的等级。原始大小是`0`，每增大或减少都表示放大20%或缩小20%，默认最大是300%，最小是50%。

### `webFrame.getZoomLevel()`

返回值为`Number`类型 - 当前缩放等级。

### `webFrame.setZoomLevelLimits(minimumLevel, maximumLevel)`

 * `minimumLevel` Number类型
 * `maximumLevel` Number类型

**不赞成使用：**调用`setVisualZoomLevelLimits`代替设置可见缩放等级界限。这个方法将在Electron2.0中被移除。

### `webFrame.setVisualZoomLevelLimits(minimumLevel, maximumLevel)`

 * `minimumLevel` Number类型
 * `maximumLevel` Number类型

设置最大和最小的缩放等级。

### `webFrame.setLayoutZoomLevelLimits(minimumLevel, maximumLevel)`

 * `minimumLevel` Number类型
 * `maximumLevel` Number类型

设置最大和最小的布局基础（即非视觉上的）缩放等级。

### `webFrame.setSpellCheckProvider(language, autoCorrectWord, provider)`

 * `language` String类型
 * `autoCorrectWord` Boolean类型
 * `provider` Object类型
     * `spellCheck` Function类型 - 返回值为`Boolean`类型
         * `text` String类型

设置一个在输入区和文本区检查拼写的供应商。

`provider`必须提供一个有`spellCheck`方法的对象，这个方法将返回单词是否拼写正确。

一个使用[node-spellchecker]()作为供应商的例子：

```javascript
const {webFrame} = require('electron')
webFrame.setSpellCheckProvider('en-US', true, {
    spellCheck (text) {
        return !(require('spellchecker').isMisspelled(text))
    }
})
```

### `webFrame.registerURLSchemeAsSecure(scheme)`

 * `scheme` String类型

注册一个`scheme`作为secure scheme.

Secure schemes不会触发混合内容警告。例如，`https`和`data`是secure schemes，因为他们不会被网络攻击活动破坏。

### `webFrame.registerURLSchemeAsBypassingCSP(scheme)`

 * `scheme` String类型

将会从这个`scheme`中加载资源，而不管当前页面的内容安全策略。

### `webFrame.registerURLSchemeAsPrivileged(scheme[, options])`

 * `scheme` String类型
 * `options` Object类型（可选参数）
     * `secure` Boolean类型（可选参数） - 默认为`true`。
     * `bypassCSP` Boolean类型（可选参数） - 默认为`true`。
     * `allowServiceWorkers` Boolean类型（可选参数） - 默认为`true`。
     * `supportFetchAPI` Boolean类型（可选参数） - 默认为`true`。
     * `corsEnabled` Boolean类型（可选参数） - 默认为`true`。

注册`scheme`为安全的，使资源绕过内容安全策略，允许注册ServiceWorker并支持获取接口。

使用`false`指定一个选项来从注册中忽略它。一个注册一个没有绕过内容安全策略的专有scheme的例子：

```javascript
const {webFrame} = require('electron')
webFrame.registerURLSchemeAsPrivileged('foo', { bypassCSP: false })
```

### `webFrame.insertText(text)`

 * `text` String类型

插入文本到焦点元素。

### `webFrame.executeJavaScript(code[, userGesture, callback])`

 * `code` String类型
 * `userGesture` Boolean (optional) - 默认是`false`
 * `callback` Function (optional) - 脚本执行后调用。
     * `result` Any

在页面中执行代码。

在浏览器窗口中，一些HTML接口，像`requestFullScreen`，只可以被用户手势触发。设置`userGesture`为`true`将解除这个限制。

### `webFrame.getResourceUsage()`

返回值为`Object`：

 * `images` [MemoryUsageDetails](https://github.com/electron/electron/blob/master/docs/api/structures/memory-usage-details.md)类型
 * `cssStyleSheets` [MemoryUsageDetails](https://github.com/electron/electron/blob/master/docs/api/structures/memory-usage-details.md)类型
 * `xslStyleSheets` [MemoryUsageDetails](https://github.com/electron/electron/blob/master/docs/api/structures/memory-usage-details.md)类型
 * `fonts` [MemoryUsageDetails](https://github.com/electron/electron/blob/master/docs/api/structures/memory-usage-details.md)类型
 * `other` [MemoryUsageDetails](https://github.com/electron/electron/blob/master/docs/api/structures/memory-usage-details.md)类型

返回一个描述Blink内部缓存的使用信息的对象。

```javascript
const {webFrame} = require('electron')
console.log(webFrame.getResourceUsage())
```

这将生成：

```javascript
{
    images: {
        count: 22,
        size: 2549,
        liveSize: 2542
    },
    cssStyleSheets: { /* same with "images" */ },
    xslStyleSheets: { /* same with "images" */ },
    fonts: { /* same with "images" */ },
    other: { /* same with "images" */ }
}
```

### `webFrame.clearCache()`

释放不再被使用的内存（例如以前导航过的图片）。

请注意，盲目的调用这个方法可能会造成Electron运行变得缓慢，直到再次填满这些空的内存，你只需要在你的应用发生一个事件使得你认为你的页面实际上使用更少的内存的时候（即你从一个庞大的页面跳转到大部分内容都为空的页面，并且打算一直保持在这个页面不动）调用这个方法。