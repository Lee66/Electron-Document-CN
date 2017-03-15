# 1.desktopCapturer

> 原文：https://github.com/electron/electron/blob/master/docs/api/desktop-capturer.md    
译者：[Lin](https://github.com/ShmilyLin)   


> 访问媒体资源的信息，可以被用来使用[navigator.webkitGetUserMedia](https://developer.mozilla.org/en/docs/Web/API/Navigator/getUserMedia)接口从桌面捕捉音频和视频。

进程：[渲染进程](../../guides/glossary-of-terms.md#renderer-process)

下面的例子演示如何从桌面上一个标题是`Electron`的窗口中捕捉视频：

    // 渲染进程中。
    const {desktopCapturer} = require('electron')

    desktopCapturer.getSources({types: ['window', 'screen']}, (error, sources) => {
        if (error) throw error
        for (let i = 0; i < sources.length; ++i) {
            if (sources[i].name === 'Electron') {
                navigator.webkitGetUserMedia({
                    audio: false,
                    video: {
                        mandatory: {
                            chromeMediaSource: 'desktop',
                            chromeMediaSourceId: sources[i].id,
                            minWidth: 1280,
                            maxWidth: 1280,
                            minHeight: 720,
                            maxHeight: 720
                        }
                    }
                }, handleStream, handleError)
                return
            }
        }
    })

    function handleStream (stream) {
        document.querySelector('video').src = URL.createObjectURL(stream)
    }

    function handleError (e) {
        console.log(e)
    }

使用[navigator.webkitGetUserMedia]()传入`desktopCapturer`提供的资源来捕捉视频，则参数重必须包含`chromeMediaSource: 'desktop'`，和`audio: false`。

## 方法

`desktopCapturer`模块有下面的方法：

### `desktopCapturer.getSources(options, callback)`

 * `options` Object类型
     * `types` String[]类型 - 一个字符串数组，列出了要捕捉的桌面资源的类型，可用类型是`screen`和`window`。
     * `thumbnailSize` Object类型（可选参数）- 建议媒体资源的缩略图应该缩放到的大小，默认是`{width: 150, height: 150}`。
 * `callback` Function类型
     * `error` Error类型
     * `sources` [DesktopCapturerSource[]](https://github.com/electron/electron/blob/master/docs/api/structures/desktop-capturer-source.md)类型

开始收集所有可用的桌面媒体资源，在收集完成后调用`callback(error, sources)`。

`sources`是一个[DesktopCapturerSource](https://github.com/electron/electron/blob/master/docs/api/structures/desktop-capturer-source.md)类型对象的数组，每一个`DesktopCapturerSource`表示可以被捕捉的一个屏幕或者一个窗口。