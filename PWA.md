# PWA

## PWA 的作用？

### 1. PWA 是什么？

PWA 示例。

### 2. PWA 的特点

#### a. 沉浸式体验。用户可以把网页添加到桌面
#### b. 可以离线缓存，达到界面极致展示。HTTP请求到的资源可以缓存起来，下次可以拦截请求，略过HTTP请求阶段。
#### c. 推送功能。推送大致是浏览器向我们服务器订阅推送，我们自己的服务器向谷歌或者火狐浏览器的Push Service发送推送消息，Push Service再把消息发送
给浏览器，浏览器再对消息进行处理。（目前尚不成熟，有待提升）

### 3. PWA 的关键技术

Servce Worker

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


