# PWA

## PWA 的作用？

### 1. PWA 是什么？

PWA并不是单指某一项技术，你更可以把它理解成是一种思想和概念，目的就是对标原生app，将Web网站通过一系列的Web技术去优化它，提升其安全性，性能，流畅性，用户体验等各方面指标，最后达到用户就像在用app一样的感觉。

### 2. PWA 的特点

#### a. 沉浸式体验。用户可以把网页添加到桌面
#### b. 可以离线缓存，达到界面极致展示。HTTP请求到的资源可以缓存起来，下次可以拦截请求，略过HTTP请求阶段。
#### c. 推送功能。推送大致是浏览器向我们服务器订阅推送，我们自己的服务器向谷歌或者火狐浏览器的Push Service发送推送消息，Push Service再把消息发送给浏览器，浏览器再对消息进行处理。（目前尚不成熟，有待提升）
#### d. 用户可以直接保存网页，不需要下载庞大的app，并且每次更新不需要重新到应用商店下载应用。

### 3. PWA 的关键技术

#### Web App Manifest

#### Service Worker

#### Cache API 缓存

#### Push&Notification 推送与通知

#### Background Sync 后台同步

#### 响应式设计

### 4. 兼容性

![image](http://www.mk2048.com/web_upload/blog_imgs/10/https___user-gold-cdn-xitu-io_2018_7_28_164df156c15abc42_w-1240_h-458_f-png_s-120965.gif)

目前大部分浏览器尤其国外浏览器兼容性还算可以，国内浏览器兼容性有待提高。

https://lavas.baidu.com/ready

## Service Work 解析

### 1. 独立的线程

我们知道 JavaScript 是单线程，虽然 Service Worker 是独立于主线程的一个线程，但是 Service Worker 也不能对 DOM 进行操作。

### 2. 离线缓存

Service Worker 可以缓存 HTTP 请求到的数据。

### 3. 拦截网络请求

Service Worker 提供了拦截网络请求的方法。可以返回本地的缓存。

### 4. 通过 postMessage 实现推送

## 劣势：

### 1. 游览器对技术支持还不够全面， 不是每一款游览器都能100%的支持所有PWA

### 2. 需要通过第三方库才能调用底层硬件（如摄像头）

### 3. PWA现在还没那么火，用户大部分都不会使用，并且很多页面也不支持
