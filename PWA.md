# PWA

## PWA介绍

苹果手机演示：
第一步，请用safri打开 weibo.cn 或者 github.com 。第二步，点击屏幕最下方中间的分享按钮，屏幕网上滑动，找到并添加到主屏幕。最后回到主屏幕，发现多了一个应用图标，可以点击进入到刚刚的网站，微博和github进入的样子会稍有不同，可以自行体验。

### 1. PWA 是什么？

PWA并不是单指某一项技术，你更可以把它理解成是一种思想和概念，目的就是对标原生app，将Web网站通过一系列的Web技术去优化它，提升其安全性，性能，流畅性，用户体验等各方面指标，最后达到用户就像在用app一样的感觉。

### 2. PWA 的特点

#### a. 沉浸式体验。用户可以把网页添加到桌面
#### b. 可以离线缓存，达到界面极致展示。HTTP请求到的资源可以缓存起来，下次可以拦截请求，略过HTTP请求阶段。
#### c. 推送功能。推送大致是浏览器向我们服务器订阅推送，我们自己的服务器向谷歌或者火狐浏览器的Push Service发送推送消息，Push Service再把消息发送给浏览器，浏览器再对消息进行处理。（目前尚不成熟，有待提升）
#### d. 用户可以直接保存网页，不需要下载庞大的app，并且每次更新不需要重新到应用商店下载应用。

## PWA 的关键技术

### 1. Web App Manifest
常用参数：

```
{
    "name": "技术分享",  //指定了Web App的名称
    "short_name": "测试", //简称，如果name展示不下，会显示这个
    "start_url": "/", //指定用户打开该Web App时加载的URL。相对URL会相对于manifest
    "display": "standalone", //fullscreen：全屏显示，会尽可能将所有的显示区域都占满；standalone：独立应用模式，这种模式下打开的应用有自己的启动图标，并且不会有浏览器的地址栏。因此看起来更像一个Native App；minimal-ui：与standalone相比，该模式会多出地址栏；browser：一般来说，会和正常使用浏览器打开样式一致
    "background_color": "#333", //在应用的样式资源为加载完毕前的默认背景，因此会展示在开屏界面。background_color加上我们定义的icons就组成了Web App打开时的“开屏图”
    "description": "简单介绍PWA相关知识",
    "orientation": "portrait-primary", //控制Web App的方向。具体的值包括：any, natural, landscape, landscape-primary, landscape-secondary, portrait, portrait-primary, portrait-secondary
    "theme_color": "#5eace0", //定义应用程序的默认主题颜色
    "icons": [{ //用来指定应用的桌面图标
        "src": "img/icons/book-32.png",
        "sizes": "32x32",
        "type": "image/png"
    }, {
        "src": "img/icons/book-72.png",
        "sizes": "72x72",
        "type": "image/png"
    }, {
        "src": "img/icons/book-128.png",
        "sizes": "128x128",
        "type": "image/png"
    }, {
        "src": "img/icons/book-144.png",
        "sizes": "144x144",
        "type": "image/png"
    }, {
        "src": "img/icons/book-192.png",
        "sizes": "192x192",
        "type": "image/png"
    }, {
        "src": "img/icons/book-256.png",
        "sizes": "256x256",
        "type": "image/png"
    }, {
        "src": "img/icons/book-512.png",
        "sizes": "512x512",
        "type": "image/png"
    }]
}
```
使用方法：
```
<!-- 在index.html中添加以下meta标签 -->
<link rel="manifest" href="/manifest.json">
```
提示：pwa-manifest-webpack-plugin 插件能够让我们在应用构建的时候生成 manifest 文件

### 2. 注册

```
// index.js
if ('serviceWorker' in window.navigator) {
  navigator.serviceWorker.register('./sw.js', { scope: './' })
    .then(function (reg) {
      console.log('success', reg);
    })
    .catch(function (err) {
      console.log('fail', err);
    });
}
```

if 用来判断浏览器是否支持 serviceWorker ，避免不兼容导致的bug。

在默认情况下，Service Worker 必定会每24小时被下载一次，如果下载的文件是最新文件，那么它就会被重新注册和安装，但不会被激活，当不再有页面使用旧的 Service Worker 的时候，它就会被激活。

所以

### 3. Cache API 缓存

### 4. Push&Notification 推送与通知

### 5. Background Sync 后台同步

### 6. 响应式设计

## 劣势：

### 1. 浏览器对技术支持还不够全面， 不是每一款游览器都能100%的支持所有PWA

![image](http://www.mk2048.com/web_upload/blog_imgs/10/https___user-gold-cdn-xitu-io_2018_7_28_164df156c15abc42_w-1240_h-458_f-png_s-120965.gif)

目前大部分浏览器尤其国外浏览器兼容性还算可以，国内浏览器兼容性有待提高。

https://lavas.baidu.com/ready

### 2. 需要通过第三方库才能调用底层硬件（如摄像头）

### 3. PWA现在还没那么火，用户大部分都不会使用，并且很多页面也不支持
