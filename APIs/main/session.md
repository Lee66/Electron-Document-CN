# 14.session

> 原文：https://github.com/electron/electron/blob/master/docs/api/session.md   
译者：[Lin](https://github.com/ShmilyLin)   


> 管理浏览器会话、cookie，缓存，代理设置等。

进程：[主进程](../../guides/glossary-of-terms.html#main-process)   

`session`模块可以被用来创建新的`Session`对象。

你也可以通过使用[`WebContents`](./webContents.html)的属性`session`来获取现存页面的`session`，或者从`session`模块获取。

    const {BrowserWindow} = require('electron')

    let win = new BrowserWindow({width: 800, height: 600})
    win.loadURL('http://github.com')

    const ses = win.webContents.session
    console.log(ses.getUserAgent())

<h2 id="methods">方法</h2>

`session`模块有以下方法：

<h3 id="session-fromPartition"><code>session.fromPartition(partition[, options])</code></h3>

 * `partition` String类型
 * `options` Object类型
     * `cache` Boolean类型 - 是否可以使用缓存。

返回值为`Session`类型 - 从`partition`字符串获取的会话。当这里存在一个拥有同样的`partition`的`Session`，它将会被返回；否则一个新的`Session`实例将会通过`options`被创建。

如果`partition`是以`persist:`开头的，这个页面将会使用一个在应用中对所有页面的相同`partition`永久有效的会话。如果没有`persist:`前缀，页面将会使用内存会话。如果`partition`是空的，那么应用的默认会话将会被返回。

使用`options`创建一个`Session`，你必须保证`Session`的`partition`在之前从来没有被使用过。这里没有方法改变一个存在的`Session`对象的`options`。

<h2 id="properties">属性</h2>

`session`模块有以下属性：

<h3 id="session-defaultSession"><code>session.defaultSession</code></h3>

一个`Session`对象，应用的默认会话对象。

<h2 id="class-session">类：Session</h2>

> 获取并设置一个会话的属性。

进程：[主进程](../../guides/glossary-of-terms.html#main-process)   

你可以使用`session`模块来创建一个新的`Session`对象。

    const {session} = require('electron')
    const ses = session.fromPartition('persist:name')
    console.log(ses.getUserAgent())

<h3 id="instance-events">实例的事件</h3>

以下事件在`Session`的实例中有效：

<h4 id="event-will-download">事件：'will-download'</h4>

 * `event` Event类型
 * `item` [DownloadItem](https://github.com/electron/electron/blob/master/docs/api/download-item.md)类型
 * `webContents` [WebContents](./webContents.html)类型

当`Electron`要下载`webContents`中的`item`的时候被分发。

调用`event.preventDefault()`将取消下载，并且`item`将从下一个进程标记起不再有效。

    const {session} = require('electron')
    session.defaultSession.on('will-download', (event, item, webContents) => {
        event.preventDefault()
        require('request')(item.getURL(), (data) => {
            require('fs').writeFileSync('/somewhere', data)
        })
    })

<h3 id="instance-methods">实例的方法</h3>

以下方法在`Session`的实例中有效：

<h4 id="ses-getCacheSize"><code>ses.getCacheSize(callback)</code></h4>

 * `callback` Function类型
     * `size` Integer类型 - 使用的缓存大小，以字节为单位。

回调会返回这个会话的当前缓存大小。

<h4 id="ses-clearCache"><code>ses.clearCache(callback)</code></h4>

 * `callback` Function类型 - 当操作完成时被调用

清空会话的HTTP缓存。

<h4 id="ses-clearStorageData"><code>ses.clearStorageData([options, callback])</code></h4>

 * `options` Object类型（可选参数）
     * `origin` String类型 - 应该遵循`window.location.origin`的表述方式`scheme://host:port`。
     * `storages` String[]类型 - 要清除的存储类型，可以包含：`appcache`、`cookies`、`filesystem`、`indexdb`、`localstorage`、`shadercache`、`websql`、`serviceworkers`
     * `quotas` String[]类型 - 要清除的量的类型，可以包含：`temporary`、`persistent`、`syncable`。
 * `callback` Function类型（可选参数）- 当操作完成时被调用。

清除网络存储数据。

<h4 id="ses-flushStorageData"><code>ses.flushStorageData()</code></h4>

将还没有写入的DOMStorage写入磁盘。

<h4 id="ses-setProxy"><code>ses.setProxy(config, callback)</code></h4>

 * `config` Object类型
     * `pacScript` String类型 - 与PAC文件相关的URL。
     * `proxyRules` String类型 - 规定要使用的代理的标志。
     * `proxyBypassRules` String类型 - 规定需要绕过代理设置的URL的标志。
 * `callback` Function类型 - 当操作完成时被调用。

配置代理设置。

当`pacScript`和`proxyRules`被一起提供，`proxyRules`选项被忽视，`pacScript`选项被应用。

`proxyRules`遵循以下规则：

    proxyRules = schemeProxies[";"<schemeProxies>]
    schemeProxies = [<urlScheme>"="]<proxyURIList>
    urlScheme = "http" | "https" | "ftp" | "socks"
    proxyURIList = <proxyURL>[","<proxyURIList>]
    proxyURL = [<proxyScheme>"://"]<proxyHost>[":"<proxyPort>]

例如：

 * `ttp=foopy:80;ftp=foopy2` - 使用HTTP代理`foopy:80`的`http://`地址，使用HTTP代理`foopy2:80`的`ftp://`地址。
 * `foopy:80` - 使用HTTP代理foopy:80的所有地址。
 * `foopy:80,bar,direct://` - 使用HTTP代理`foopy:80`的所有地址，如果`foopy:80`不可用则换为`bar`，如果`bar`也不可用那就不使用代理。
 * `socks4://foopy` - 使用SOCKS v4代理`foopy:1080`的所有地址。
 * `http=foopy,socks5://bar.com` - 使用HTTP代理`foopy`的所有http地址，如果`foopy`不可用就换为使用SOCKS5代理`bar.com`。
 * `http=foopy,direct://` - 使用HTTP代理`foopy`的所有http地址，如果`foopy`不可用就不使用代理。
 * `http=foopy;socks=foopy2` - 使用HTTP代理`foopy`的所有http地址，并使用socks4://foopy2代理其他所有的地址。

`proxyBypassRules`是一个使用逗号分隔的列表，规则描述见下面：

 * `[ URL_SCHEME "://" ] HOSTNAME_PATTERN [ ":" <port> ]`

    匹配所有hostnames that match the pattern HOSTNAME_PATTERN.

    例如："foobar.com", "foobar.com", ".foobar.com", "foobar.com:99", "https://x..y.com:99"

         * `"." HOSTNAME_SUFFIX_PATTERN [ ":" PORT ]`

        匹配一个特定的后缀范围。

    例如：".google.com", ".com", "http://.google.com"

 * `[ SCHEME "://" ] IP_LITERAL [ ":" PORT ]`

    匹配符合IP地址字面量的地址。

    例如："127.0.1", "[0:0::1]", "[::1]", "http://[::1]:99"

 * `IP_LITERAL "/" PREFIX_LENGHT_IN_BITS`

    Match any URL that is to an IP literal that falls between the given range. IP范围使用CIDR符号指定。

    例如："192.168.1.1/16", "fefe:13::abc/33".

 * `<local>`

    匹配本地地址。`<local>`的意思是匹配到这之中的任何一个："127.0.0.1"，"::1"，"localhost"。


<h4 id="ses-resolveProxy"><code>ses.resolveProxy(url, callback)</code></h4>

 * `url` URL类型
 * `callback` Function类型
     * `proxy` String类型

解析`url`的协议信息。当请求被执行的时候`callback`将要被`callback(proxy)`调用。

<h4 id="ses-setDownloadPath"><code>ses.setDownloadPath(path)</code></h4>

 * `path` String类型 - 下载位置

设置下载保存目录。默认情况下，下载目录将是在各自应用的文件夹中的`Downloads`文件夹下。

<h4 id="ses-enableNetworkEmulation"><code>ses.enableNetworkEmulation(options)</code></h4>

 * `options` Object类型
     * `offline` Boolean类型（可选参数）- 是否模拟网络断开。默认为`false`。
     * `latency` Double类型（可选参数）- ms为单位的RTT。默认是0，即禁止限制延迟。
     * `downloadThroughput` Double类型（可选参数）- Bps为单位的下载速度。默认是0，即禁止限制下载。
     * `uploadThroughput` Double类型（可选参数）- Bps为单位的上传速度。默认是0，即禁止限制上传。

`session`中使用给定的配置模拟网络。

    // 使用50kbps吞吐量和500ms延迟来模拟一个GPRS链接。
    window.webContents.session.enableNetworkEmulation({
        latency: 500,
        downloadThroughput: 6400,
        uploadThroughput: 6400
    })

    // 模拟一个网络中断。
    window.webContents.session.enableNetworkEmulation({offline: true})

<h4 id="ses-disableNetworkEmulation"><code>ses.disableNetworkEmulation()</code></h4>

禁用`session`中已经开启的网络模拟。将重置为原来的网络配置。

<h4 id="ses-setCertificateVerifyProc"><code>ses.setCertificateVerifyProc(proc)</code></h4>

 * `proc` Function类型
     * `request` Object类型
         * `hostname` String类型
         * `certificate` Certificate类型
         * `error` String类型 - 从chromium中验证结果。
     * `callback` Function类型
         * `verificationResult` Integer类型 - 值可以是[这里](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)面的证书错误代码之一。除了证书错误代码之外还可以使用下面的特殊代码。
             * `0` - 表示成功，并且不允许Certificate Transperancy验证。
             * `-2` - 表示失败。
             * `-3` - 使用chromium的验证结果。

设置`session`的证书验证过程，当一个服务器证书验证被请求，`proc`将会被`proc(request, callback)`调用 whenever a server certificate verification is requested. 调用`callback(0)`接受这个证书，调用`callback(-2)`拒绝这个证书。

调用`setCertificateVerifyProc(null)`将会恢复回默认的证书验证过程。

    const {BrowserWindow} = require('electron')
    let win = new BrowserWindow()

    win.webContents.session.setCertificateVerifyProc((request, callback) => {
        const {hostname} = request
        if (hostname === 'github.com') {
            callback(0)
        } else {
            callback(-2)
        }
    })

<h4 id="ses-setPermissionRequestHandler"><code>ses.setPermissionRequestHandler(handler)</code></h4>

 * `handler` Function类型
     * `webContents` Object类型 - [WebContents](./webContents.html)请求许可。
     * `permission` String类型 - 枚举值为'media'，'geolocation'，'notifications'，'midiSysex'，'pointerLock'，'fullscreen'，'openExternal'。
     * `callback` Function类型
         * `permissionGranted` Boolean类型 - 允许或者拒绝这个许可。

设置`session`的许可请求的回复处理。调用`callback(true)`将允许这个许可，调用`callback(false)`将拒绝这个许可。

    const {session} = require('electron')
    session.fromPartition('some-partition').setPermissionRequestHandler((webContents, permission, callback) => {
        if (webContents.getURL() === 'some-host' && permission === 'notifications') {
            return callback(false) // denied.
        }

        callback(true)
    })

<h4 id="ses-clearHostResolverCache"><code>ses.clearHostResolverCache([callback])</code></h4>

 * `callback` Function类型（可选参数）- 当操作完成时被调用。

清空主机解析器的缓存。

<h4 id="ses-allowNTLMCredentialsForDomains"><code>ses.allowNTLMCredentialsForDomains(domains)</code></h4>

 * `domains` String类型 - A comma-seperated list of servers for which integrated authentication is enabled.

Dynamically sets whether to always send credentials for HTTP NTLM or Negotiate authentication.

    const {session} = require('electron')
    // 考虑到任何使用`example.com`, `foobar.com`, `baz`结尾的url的集成认证。
    session.defaultSession.allowNTLMCredentialsForDomains('*example.com, *foobar.com, *baz')

    // 考虑到所有的url的集成认证。
    session.defaultSession.allowNTLMCredentialsForDomains('*')

<h4 id="ses-setUserAgent"><code>ses.setUserAgent(userAgent[, acceptLanguages])</code></h4>

 * `userAgent` String类型
 * `acceptLanguages` String类型（可选参数）

重写这个会话的`userAgent`和`acceptLanguages`。

`acceptLanguages`必须是一个逗号分隔的语言代码的命令列表，例如：`"en-US,fr,de,ko,zh-CN,ja"`。

这个不能影响到当前存在的`WebContents`，每个`WebContents`可以使用`webContents.setUserAgent`来重写会话范围内的用户代理。

<h4 id="ses-getUserAgent"><code>ses.getUserAgent()</code></h4>

返回值为`String`类型 - 这个会话的用户代理

<h4 id="ses-getBlobData"><code>ses.getBlobData(identifier, callback)</code></h4>

 * `identifier` String类型 - 有效的UUID。
 * `callback` Function类型
     * `result` Buffer类型 - Blob数据。

返回值为`Blob`类型 - 与`identifier`相关的blob数据。

<h4 id="ses-createInterruptedDownload"><code>ses.createInterruptedDownload(options)</code></h4>

 * `options` Object类型
     * `path` String类型 - 下载的绝对路径。
     * `urlChain` String[]类型 - 完整的下载链接。
     * `mimeType` String类型（可选参数）
     * `offset` Integer类型 - 下载的起始范围。
     * `length` Integer类型 - 下载的总长度。
     * `lastModified` String类型 - 最后修改头的值。
     * `eTag` String类型 - ETag头的值。
     * `startTime` Double类型（可选参数）- Time when download was started in number of seconds since UNIX epoch.

允许在之前的`Session`中重新开始`cancelled`或`interrupted`下载。这个接口将生成一个可以被[will-download](#event-will-download)事件使用的[DownloadItem]()https://github.com/electron/electron/blob/master/docs/api/download-item.md。这个[DownloadItem](https://github.com/electron/electron/blob/master/docs/api/download-item.md)将没有任何和它相关的`WebContents`，并且最开始的状态将会被`interrupted`。这个下载将只在[DownloadItem](https://github.com/electron/electron/blob/master/docs/api/download-item.md)中调用`resume`接口时启动。

<h4 id="ses-clearAuthCache"><code>ses.clearAuthCache(options[, callback])</code></h4>

 * `options` ([RemovePassword](https://github.com/electron/electron/blob/master/docs/api/structures/remove-password.md) | [RemoveClientCertificate](https://github.com/electron/electron/blob/master/docs/api/structures/remove-client-certificate.md))类型
 * `callback` Function类型（可选参数）- 当操作完成时被调用。

清空会话的HTTP验证缓存。

<h3 id="instance-properties">实例的属性</h3>

以下属性在`Session`的实例中有效：

<h4 id="ses-cookies"><code>ses.cookies</code></h4>

这个会话的Cookie对象。

<h4 id="ses-webRequest"><code>ses.webRequest</code></h4>

这个会话的WebRequest对象。

<h4 id="ses-protocol"><code>ses.protocol</code></h4>

这个会话的Protocol对象（一个[protocol](./protocol.md)模块的实例）。

    const {app, session} = require('electron')
    const path = require('path')

    app.on('ready', function () {
        const protocol = session.fromPartition('some-partition').protocol
        protocol.registerFileProtocol('atom', function (request, callback) {
            var url = request.url.substr(7)
            callback({path: path.normalize(`${__dirname}/${url}`)})
        }, function (error) {
            if (error) console.error('Failed to register protocol')
        })
    })
