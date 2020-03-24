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

## PWA 的技术实现

### 1. Web App Manifest

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

if 用来判断浏览器是否支持 serviceWorker ，避免不兼容导致的bug，达到了渐进增强的目的。

在默认情况下，Service Worker 必定会每24小时被下载一次，如果下载的文件是最新文件，那么它就会被重新注册和安装，但不会被激活，当不再有页面使用旧的 Service Worker 的时候，它就会被激活。

这对于我们开发来说是很不方便的，因此在这里我勾选了一个名为 Update on reload 的单选框，选中它之后，我们每次刷新页面都能够使用最新的 service worker 文件。

在同一个 Origin 下，我们可以注册多个 Service Worker。但是请注意，这些 Service Worker 所使用的 scope 必须是不相同的。

### 3. 通讯

#### 首先是从页面发送信息到 Serivce Worker 。

```
// index.js
if ('serviceWorker' in window.navigator) {
  navigator.serviceWorker.register('./sw.js', { scope: './' })
    .then(function (reg) {
      console.log('success', reg);
      navigator.serviceWorker.controller && navigator.serviceWorker.controller.postMessage("this message is from page");
    });
}
```

为了保证 Service Worker 能够接收到信息，我们在它被注册完成之后再发送信息，和普通的 window.postMessage 的使用方法不同，为了向 Service Worker 发送信息，我们要在 ServiceWorker 实例上调用 postMessage 方法，这里我们使用到的是 navigator.serviceWorker.controller 。

```
// sw.js
this.addEventListener('message', function (event) {
  console.log(event.data); // this message is from page
});
```

在 service worker 文件中我们可以直接在 this 上绑定 message 事件，这样就能够接收到页面发来的信息了。

对于不同 scope 的多个 Service Worker ，我么也可以给指定的 Service Worker 发送信息。

#### 从 Service Worker 到页面

下一步就是从 Service Worker 发送信息到页面了，不同于页面向 Service Worker 发送信息，我们需要在 WindowClient 实例上调用 postMessage 方法才能达到目的。而在页面的JS文件中，监听 navigator.serviceWorker 的 message 事件即可收到信息。

而最简单的方法就是从页面发送过来的消息中获取 WindowClient 实例，使用的是 event.source ，不过这种方法只能向消息的来源页面发送信息。

```
// sw.js
this.addEventListener('message', function (event) {
  event.source.postMessage('this message is from sw.js, to page');
});

// index.js
navigator.serviceWorker.addEventListener('message', function (e) {
  console.log(e.data); // this message is from sw.js, to page
});
```

如果不想受到这个限制，则可以在 serivce worker 文件中使用 this.clients 来获取其他的页面，并发送消息。

```
// sw.js
this.clients.matchAll().then(client => {
  client[0].postMessage('this message is from sw.js, to page');
})
```

### 4. 缓存

缓存指定静态资源

```
// sw.js
this.addEventListener('install', function (event) {
  console.log('install');
  event.waitUntil(
    caches.open('sw_demo').then(function (cache) {
      return cache.addAll([
        '/style.css',
        '/panda.jpg',
        './main.js'
      ])
    }
    ));
});
```

当 Service Worker 在被安装的时候，我们能够对制定路径的资源进行缓存。CacheStroage 在浏览器中的接口名是 caches ，我们使用 caches.open 方法新建或打开一个已存在的缓存；cache.addAll 方法的作用是请求指定链接的资源并把它们存储到之前打开的缓存中。由于资源的下载、缓存是异步行为，所以我们要使用事件对象提供的 event.waitUntil 方法，它能够保证资源被缓存完成前 Service Worker 不会被安装完成，避免发生错误。

从 Chrome 开发工具中的 Application 的 Cache Strogae 中可以看到我们缓存的资源。

动态缓存静态资源
```
this.addEventListener('fetch', function (event) {
  console.log(event.request.url);
  event.respondWith(
    caches.match(event.request).then(res => {
      return res ||
        fetch(event.request)
          .then(responese => {
            const responeseClone = responese.clone();
            caches.open('sw_demo').then(cache => {
              cache.put(event.request, responeseClone);
            })
            return responese;
          })
          .catch(err => {
            console.log(err);
          });
    })
  )
});
```
在回掉函数中我们使用事件对象提供的 respondWith 方法，它可以劫持用户发出的 http 请求，并把一个 Promise 作为响应结果返回给用户。然后我们使用用户的请求对 Cache Stroage 进行匹配，如果匹配成功，则返回存储在缓存中的资源；如果匹配失败，则向服务器请求资源返回给用户，并使用 cache.put 方法把这些新的资源存储在缓存中。因为请求和响应流只能被读取一次，所以我们要使用 clone 方法复制一份存储到缓存中，而原版则会被返回给用户

注意：

1. 用户第一次访问页面的时候，资源的请求是早于 Service Worker 的安装的，所以静态资源是无法缓存的；只有当 Service Worker 安装完毕，用户第二次访问页面的时候，这些资源才会被缓存起来；
2. Cache Stroage 只能缓存静态资源，所以它只能缓存用户的 GET 请求；
3. Cache Stroage 中的缓存不会过期，但是浏览器对它的大小是有限制的，所以需要我们定期进行清理；

更新 Cache Stroage
前面提到过，当有新的 service worker 文件存在的时候，他会被注册和安装，等待使用旧版本的页面全部被关闭后，才会被激活。这时候，我们就需要清理下我们的 Cache Stroage 了，删除旧版本的 Cache Stroage 。
this.addEventListener('install', function (event) {
  console.log('install');
  event.waitUntil(
    caches.open('sw_demo_v2').then(function (cache) { // 更换 Cache Stroage
      return cache.addAll([
        '/style.css',
        '/panda.jpg',
        './main.js'
      ])
    }
    ))
});

const cacheNames = ['sw_demo_v2']; // Cahce Stroage 白名单

this.addEventListener('activate', function (event) {
  event.waitUntil(
    caches.keys().then(keys => {
      return Promise.all[keys.map(key => {
        if (!cacheNames.includes(key)) {
          console.log(key);
          return caches.delete(key); // 删除不在白名单中的 Cache Stroage
        }
      })]
    })
  )
});

### 5. Background Sync 后台同步

### 6. 响应式设计

## 劣势：

### 1. 浏览器对技术支持还不够全面， 不是每一款游览器都能100%的支持所有PWA

![image](http://www.mk2048.com/web_upload/blog_imgs/10/https___user-gold-cdn-xitu-io_2018_7_28_164df156c15abc42_w-1240_h-458_f-png_s-120965.gif)

目前大部分浏览器尤其国外浏览器兼容性还算可以，国内浏览器兼容性有待提高。

https://lavas.baidu.com/ready

### 2. 需要通过第三方库才能调用底层硬件（如摄像头）

### 3. PWA现在还没那么火，用户大部分都不会使用，并且很多页面也不支持
