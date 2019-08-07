---
title: ServiceWorker
date: 2019-04-12 10:24:39
tags: Project
---

### Service Worker 生命周期

```
Service Worker 脚本通过 navigator.serviceWorker.register 方法注册到页面。Service Worker 从注册开始需要先 install, 如果 install 成功, 接下来需要 activate, 然后才能接管页面。但是如果页面被先前的 Service Worker 控制着, 那么它会停留在 installed(waiting) 这个阶段等到页面重新打开才会接管页面, 或者可以通过调用 self.skipWaiting() 方法跳过等待
```

- 过程：
  - parsed：注册完成，脚本解析成功，尚未安装
  - installing：对应Service Worker脚本install事件，如果事件里有event.waitUntil()则会等待传入的Promise完成才会成功
  - installed(waiting): 页面被旧的 Service Worker 脚本控制, 所以当前的脚本尚未激活。可以通过 self.skipWaiting() 激活新的 Service Worker
  - activating: 对应 Service Worker 脚本 activate 事件执行, 如果事件里有 event.waitUntil() 则会等待这个 Promise 完成才会成功。这时可以调用 Clients.claim() 接管页面
  - activated: 激活成功, 可以处理 fetch, message 等事件
  - redundant: 安装失败, 或者激活失败, 或者被新的 Service Worker 替代掉

```
Service Worker脚本最常用的功能是截获请求和缓存资源文件，可绑定在这些事件上：

- install事件中，抓取资源进行缓存；
- activate事件中，遍历缓存，清除过期的资源；
- fetch事件中，拦截请求，查询缓存或网络，返回请求的资源。
```

1. 缓存策略：
   处在 activated 状态的 Service Worker 可以拦截作用范围下的页面的网络请求, 由 Service Worker 监听 fetch 事件来决定请求如何响应

### 全局变量

```
self: 表示 Service Worker 作用域, 也是全局变量
caches: 表示缓存，用来缓存外部资源
skipWaiting: 表示强制当前处在 waiting 状态的脚本进入 activate 状态
clients: 表示 Service Worker 接管的页面
```