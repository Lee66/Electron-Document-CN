# 13.protocol

> 原文：https://github.com/electron/electron/blob/master/docs/api/protocol.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 注册一个自定义协议，并且拦截现有协议的请求

进程：[主进程](../../guides/glossary-of-terms.md#main-process)   

执行一个协议的例子，对`file://`协议同样有效：

```javascript
const {app, protocol} = require('electron')
const path = require('path')

app.on('ready', () => {
    protocol.registerFileProtocol('atom', (request, callback) => {
        const url = request.url.substr(7)
        callback({path: path.normalize(`${__dirname}/${url}`)})
    }, (error) => {
        if (error) console.error('Failed to register protocol')
    })
})
```

**注意：**除特殊说明的方法之外，所有方法都必须在`app`模块分发`ready`事件之后使用。

## 方法

`protocol`模块有以下方法：

### `protocol.registerStandardSchemes(schemes[, options])`

 * `schemes` String[]类型 - 以标准scheme注册自定义scheme。
 * `options` Object类型（可选参数）
     * `secure` Boolean类型（可选参数）- `true`表示安全的注册scheme。否则则是`false`。

一个标准的scheme遵循RFC 3986的[generic URI syntax]()。例如`http`和`https`是标准scheme，而`file`则不是。

注册一个scheme作为标准，当开启服务时将允许相关的和完全一致的资源被正确的解析。否则scheme将会像`file`协议一样，没有资格解析相应的URL。

例如当你使用没有注册为标准scheme的自定义协议加载下面的页面时，图片将不会被加载，因为不标准schemes将无法识别相应的URL：

```javascript
<body>
    <img src='test.png'>
</body>
```

注册一个scheme作为标准将允许通过[FileSystem API](https://developer.mozilla.org/en-US/docs/Web/API/LocalFileSystem)使用文件。否则渲染器将抛出一个安全的scheme的错误。

默认情况下，网页存储接口（localStorage, sessionStorage, webSQL, indexedDB, cookies）在不标准的scheme中是不允许使用的。所以通常情况下，如果你想注册一个自定义协议来代替`http`协议，你需要注册它为一个标准的scheme：

```javascript
const {app, protocol} = require('electron')

protocol.registerStandardSchemes(['atom'])
app.on('ready', () => {
    protocol.registerHttpProtocol('atom', '...')
})
```

**注意：**这个方法只可以在`app`模块分发`ready`事件之前使用。

### `protocol.registerServiceWorkerSchemes(schemes)`

 * `schemes` String[]类型 - 注册自定义scheme来处理service workers。

### `protocol.registerFileProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` [UploadData[]](https://github.com/electron/electron/blob/master/docs/api/structures/upload-data.md)类型
     * `callback` Function类型
         * `filePath` String类型（可选参数）
 * `completion` Function类型（可选参数）
     * `error` Error类型

注册`scheme`中的协议，该协议将会发送文件作为响应。当一个`request`被使用`scheme`创建，`handler`将会被使用`handler(request, callback)`调用。当`scheme`被成功注册将会被使用`completion(null)`调用，当`scheme`注册失败将会被使用`completion(error)`调用。

为了处理`request`，`callback`需要被使用文件的路径或者一个拥有`path`属性的对象来调用，例如`callback(filePath)`或`callback({path: filePath})`。

当`callback`没有被使用任何值或使用一个数值或一个拥有`error`属性的对象而调用的时候，将认为你指定的`error`数值是`request`失败的原因。对于你可以使用的错误数值，你可以查看[net error list](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)。

默认情况下，`scheme`是被处理为类似`http:`，像`file:`一样遵循"generic URI syntax"来分析不同的协议，所以你可能想调用`protocol.registerStandardSchemes`来使你的scheme被处理为一个标准scheme。

### `protocol.registerBufferProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` [UploadData[]](https://github.com/electron/electron/blob/master/docs/api/structures/upload-data.md)类型
     * `callback` Function类型
         * `buffer` (Buffer | [MimeTypedBuffer](https://github.com/electron/electron/blob/master/docs/api/structures/mime-typed-buffer.md))类型（可选参数）
 * `completion` Function类型（可选参数）
     * `error` Error类型

注册`scheme`中的协议，该协议将会发送一个`Buffer`作为响应。

使用上类似于`registerFileProtocol`，除了`callback`需要被使用`Buffer`对象或者一个拥有`data`、`mimeType`和`charset`属性的对象来调用。

例如：

```javascript
const {protocol} = require('electron')

protocol.registerBufferProtocol('atom', (request, callback) => {
  callback({mimeType: 'text/html', data: new Buffer('<h5>Response</h5>')})
}, (error) => {
  if (error) console.error('Failed to register protocol')
})
```

### `protocol.registerStringProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` [UploadData[]](https://github.com/electron/electron/blob/master/docs/api/structures/upload-data.md)类型
     * `callback` Function类型
         * `data` String类型（可选参数）
 * `completion` Function类型（可选参数）
     * `error` Error类型

注册`scheme`中的协议，该协议将会发送一个`String`作为响应。

使用上类似于`registerFileProtocol`，除了`callback`需要被使用`String`对象或者一个拥有`data`、`mimeType`和`charset`属性的对象来调用。

### `protocol.registerHttpProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` UploadData[]类型
     * `callback` Function类型
         * `redirectRequest` Object类型
             * `url` String类型
             * `method` String类型
             * `session` Object类型（可选参数）
             * `uploadData` Object类型（可选参数）
                 * `contentType` String类型 - 内容的MIME类型。
                 * `data` String类型 - 要发送的内容。
 * `completion` Function类型（可选参数）
     * `error` Error类型

注册`scheme`中的协议，该协议将会发送一个HTTP请求作为响应。

使用上类似于`registerFileProtocol`，除了`callback`需要被使用`redirectRequest`对象或者一个拥有`url`、`method`、`referrer`、`uploadData`和`session`属性的对象来调用。

默认情况下，HTTP请求将重用当前会话。如果你想要请求一个不同的胡话，你需要设置`session`为`null`。

对于POST请求，必须提供`uploadData`对象。

### `protocol.unregisterProtocol(scheme[, completion])`

 * `scheme` String类型
 * `completion` Function类型（可选参数）
     * `error` Error类型

取消注册`scheme`的自定义协议。

### `protocol.isProtocolHandled(scheme, callback)`

 * `scheme` String类型
 * `callback` Function类型
     * `error` Error类型

`callback`将会被使用一个布尔值调用，来表明这事是否有一个对`scheme`的处理。

### `protocol.interceptFileProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` [UploadData[]](https://github.com/electron/electron/blob/master/docs/api/structures/upload-data.md)类型
     * `callback` Function类型
         * `filePath` String类型
 * `completion` Function类型（可选参数）
     * `error` Error类型

拦截`scheme`协议，并使用`handler`作为这个协议的心得处理器，将发送一个文件作为返回值。

### `protocol.interceptStringProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` [UploadData[]](https://github.com/electron/electron/blob/master/docs/api/structures/upload-data.md)类型
     * `callback` Function类型
         * `data` String类型（可选参数）
 * `completion` Function类型（可选参数）
     * `error` Error类型

拦截`scheme`协议，并使用`handler`作为这个协议的心得处理器，将发送一个`String`作为返回值。

### `protocol.interceptBufferProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` [UploadData[]](https://github.com/electron/electron/blob/master/docs/api/structures/upload-data.md)类型
     * `callback` Function类型
         * `buffer` Buffer类型（可选参数）
 * `completion` Function类型（可选参数）
     * `error` Error类型

拦截`scheme`协议，并使用`handler`作为这个协议的心得处理器，将发送一个`Buffer`作为返回值。

### `protocol.interceptHttpProtocol(scheme, handler[, completion])`

 * `scheme` String类型
 * `handler` Function类型
     * `request` Object类型
         * `url` String类型
         * `referrer` String类型
         * `method` String类型
         * `uploadData` UploadData[]类型
     * `callback` Function类型
         * `redirectRequest` Object类型
             * `url` String类型
             * `method` String类型
             * `session` Object类型（可选参数）
             * `uploadData` Object类型（可选参数）
                 * `contentType` String类型 - 内容的MIME类型。
                 * `data` String类型 - 要发送的内容。
 * `completion` Function类型（可选参数）
     * `error` Error类型

拦截`scheme`协议，并使用`handler`作为这个协议的心得处理器，将发送一个新的HTTP请求作为返回值。

### `protocol.uninterceptProtocol(scheme[, completion])`

 * `scheme` String类型
 * `completion` Function类型（可选参数）
     * `error` Error类型

移除一个已经安装的`scheme`的拦截器，并且恢复它为原始的处理方式。