# PWA

## PWA介绍

PWA 全称是 Progressive Web App ，即渐进式WEB应用。

如何打开：

第一步，请用safri打开 weibo.cn 或者 github.com 。第二步，点击屏幕最下方中间的分享按钮，屏幕网上滑动，找到并添加到主屏幕。最后回到主屏幕，发现多了一个应用图标，可以点击进入到刚刚的网站，微博和github进入的样子会稍有不同，可以自行体验。

### 1. PWA 是什么？

PWA 首先是一个网页，可以通过WEB技术编写，随后加入App Mainfest 和 Service Worker 来实现 PWA 的安装和离线使用等功能。

### 2. PWA 的特点

a. 沉浸式体验。可以添加到主屏幕。

b. 可以离线缓存，达到界面极致展示。HTTP请求到的资源可以缓存起来，下次可以拦截请求，略过HTTP请求阶段。

c. 推送功能。推送大致是浏览器向我们服务器订阅推送，我们自己的服务器向谷歌或者火狐浏览器的Push Service发送推送消息，Push Service再把消息发送给浏览器，浏览器再对消息进行处理。（目前尚不成熟，有待提升）

d. 用户可以直接保存网页，不需要下载庞大的app，并且每次更新不需要重新到应用商店下载应用。

## PWA 的技术实现

### 一. Manifest
使用方法：
```
<!-- 在index.html中添加以下meta标签 -->
<link rel="manifest" href="/manifest.json">
```
提示：pwa-manifest-webpack-plugin 插件能够让我们在应用构建的时候生成 manifest 文件

具体配置：
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

### 二. Service Worker 的使用

SW 的生命周期 (Regist ->（Download）-> Install -> Actived)


### 1. 注册 Service Worker

由于兼容性的要求，可以把 PWA 当做渐进增强的一个功能。

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

注意： scope 是控制当前的作用域范围，相同作用域如果旧的sw正在使用，则不会用新的覆盖。
另外需要注意，sw.js 最好增加时间戳，保证 Servce Worker 是最新版本。 另外，sw.js 在浏览器缓存层面最好也使用 no-cache 的手段增加保障。

PWA是异步安装，不会影响 主线程 和 渲染线程，不能操作dom。所以 Service Worker 就有和主线程通讯的需求了

### 2. 通讯功能

#### 首先是从主页面发送信息到 Service Worker 。

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

这里的缓存不是我们平时见到的 HTTP 缓存。

另外有一点需要注意的是，出于对安全问题的考虑，Service Worker 只能被使用在 https 或者本地的 localhost 环境下。

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

动态缓存
```
this.addEventListener('fetch', function (event) {
  console.log(event.request.url);
  event.respondWith(    //劫持请求
    caches.match(event.request).then(res => {
      return res || //如果res有值，说明命中，则直接返回，否则则调用http请求
        fetch(event.request)
          .then(responese => {
            const responeseClone = responese.clone();
            caches.open('sw_demo').then(cache => {
              // event.request.method === "POST" 注意，这里可以增加一句，如果是POST请求，则不需要缓存，POST请求会修改数据，所以缓存POST请求意义不大
              cache.put(event.request, responeseClone); //把请求存进缓存中
            })
            return responese;   //返回请求
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
3. Cache Stroage 中的缓存不会过期。

更新 Cache Stroage
前面提到过，当有新的 service worker 文件存在的时候，他会被注册和安装，等待使用旧版本的页面全部被关闭后，才会被激活。这时候，我们就需要清理下我们的 Cache Stroage 了，删除旧版本的 Cache Stroage 。

```
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
```

## 劣势：

### 1. 浏览器对技术支持还不够全面， 不是每一款游览器都能100%的支持所有PWA

![image](http://www.mk2048.com/web_upload/blog_imgs/10/https___user-gold-cdn-xitu-io_2018_7_28_164df156c15abc42_w-1240_h-458_f-png_s-120965.gif)

目前大部分浏览器尤其国外浏览器兼容性还算可以，国内浏览器兼容性有待提高。

https://lavas.baidu.com/ready

### 2. 需要通过第三方库才能调用底层硬件（如摄像头）

### 3. PWA现在还没那么火，用户大部分都不会使用，并且很多页面也不支持
