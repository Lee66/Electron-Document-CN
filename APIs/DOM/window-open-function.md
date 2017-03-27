# 3.window.open Function

> 原文：https://github.com/electron/electron/blob/master/docs/api/window-open.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 打开一个新的窗口并加载一个URL。   

当`window.open`在一个网页中被调用来打开一个新的窗口，一个新的`BrowserWindow`实例将会依据`url`被创建， and a proxy will be returned to `window.open` to let the page have limited control over it.

The proxy has limited standard functionality implemented to be compatible with traditional web pages. For full control of the new window you should create a `BrowserWindow` directly.

The newly created `BrowserWindow` will inherit the parent window's options by default. To override inherited options you can set them in the `features` string.

### `window.open(url[, frameName][, features])`

 * `url` String类型
 * `frameName` String类型（可选参数）
 * `features` String类型（可选参数）

返回值为[`BrowserWindowProxy`]()类型 - Creates a new window and returns an instance of `BrowserWindowProxy` class.

The `features` string follows the format of standard browser, but each feature has to be a field of `BrowserWindow`'s options.

**注意：**

 * `Node` integration will always be disabled in the opened `window` if it is disabled on the parent window.
 * `Non-standard` features (that are not handled by Chromium or Electron) given in `features` will be passed to any registered `webContent`'s `new-window` event handler in the `additionalFeatures` argument.


### `window.opener.postMessage(message, targetOrigin)`

 * `message` String类型
 * `targetOrigin` String类型

Sends a message to the parent window with the specified origin or `*` for no origin preference.