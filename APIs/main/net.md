# 10.net

> 原文：https://github.com/electron/electron/blob/master/docs/api/net.md    
译者：[Lin](https://github.com/ShmilyLin)   

> 使用Chromium的原生网络库发出HTTP/HTTPS请求。

进程：[主进程](../../guides/glossary-of-terms.html#main-process)

`net`模块是一个发送HTTP(S)请求的客户端接口。它类似于Node.js中的[HTTP](https://nodejs.org/api/http.html)和[HTTPS](https://nodejs.org/api/https.html)模块，但是使用Chromium的原生网络库代替Node.js的实现，可以提供更好的网络代理。

下面是为什么你可以使用`net`模块代替本地Node.js模块的不完全的理由的列表：

 * 自动管理系统代理配置，支持自动发现协议和代理PAC配置文件。
 * Automatic tunneling of HTTPS requests.
 * 验证协议支持使用`basic`，`digest`，`NTLM`，`Kerberos`或`negotiate`的认证方式。
 * 支持交换控制协议：Fiddler-like代理用于监控和操作。

`net`模块接口已经特别的设计用于模仿，尽可能的接近熟悉的Node.js接口。这个接口组成包括类，方法，属性和事件名称都和通常使用的Node.js接口相似。

例如，下面的例子快速展示了`net`接口如何被使用：

    const {app} = require('electron')
    app.on('ready', () => {
        const {net} = require('electron')
        const request = net.request('https://github.com')
        request.on('response', (response) => {
            console.log(`STATUS: ${response.statusCode}`)
            console.log(`HEADERS: ${JSON.stringify(response.headers)}`)
            response.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`)
            })
            response.on('end', () => {
                console.log('No more data in response.')
            })
        })
        request.end()
    })

顺便说一下，这个和你正常使用Node.js的[HTTP](https://nodejs.org/api/http.html)／[HTTPS](https://nodejs.org/api/https.html)模块几乎完全类似。

`net`接口只可以在应用分发`ready`事件之后使用。如果试图在`ready`事件之前使用将会抛出一个错误。

<h2 id="methods">Methods</h2>

`net`模块有下面的方法：

<h3 id="net-request"><code>net.request(options)</code></h3>

 * `options` (Object | String)类型 - `ClientRequest`构造器选项。

返回值为[`ClientRequest`](https://github.com/electron/electron/blob/master/docs/api/client-request.md)类型

使用提供的`options`创建一个[`ClientRequest`](https://github.com/electron/electron/blob/master/docs/api/client-request.md)实例，`options`将会被直接转发给`ClientRequest`构造器。`net.request`方法将会根据在`options`中指定的协议方式被用来发送安全和不安全的HTTP请求。