---
title: Node内存泄露
date: 2019-04-12 10:20:36
tags: Node
---

### Node内存泄露

- 问题：

  我们的应用中有时响应会很慢，甚至没有响应

  每天访问量在特定时间段会有大范围波动，但是整体比较平稳 。

- 分析：

  正常情况下node发起的请求，服务端就会有响应，最后吐给前端

  利用inspect反复分析内存，经过代码追踪，请求受阻，settimeout 代码不执行，被挂起，无法释放

  axios 超时

```
对于node来说，（代码块被挂起）cpu上升的原因有：settimeout，IO阻塞，express没调用res.end()结束请求。

压测后发现的现象：在服务器超载的情况下，由于无法做出响应，客户端的socket就会被挂起一直处于connection状态。
```

- 结论：

  有时，响应会很慢，并且没有响应，就是连接事件将被事件循环系统阻塞。

  由于Node中，io链接会阻塞timer处理，因此这个setTimeout不会按时触发，也就有了超过了设定的超时时间以上才返回的情况。阻塞的connection导致请求堆积，服务器处理不过来，CPU也就下不来。

- 解决：

利用socket中对于connect的超时处理来代替会在Nodejs中被阻塞的setTimeout来处理超时请求。

另一个暴力方案，采用pm2 start app.js –max-memory-restart 20M，声明应用在超过使用内存上限后自动重启

### 总结

在vue-ssr中造成内存和cpu泄露的原因：

- 挂起的socket造成的暂时性的堵塞
- vue-router中的timer在某些情况下会陷入死循环
- 大量的模板编译，内存中会存留大量被字符串占用的内存

解决方案：

- 移除component中对于beforeRouteEnter的处理。将这里的处理移到其他地方，从vue-router代码层面分析是可以避免陷入timer的死循环的。
- 在nodejs中替换掉setTimeout的方式去处理服务器端请求超时，改用http.request的timeout事件handle来处理。防止io阻塞timer处理。
- 如果不是对seo要求过高，采用骨架页渲染的方式，向客户端渲染出骨架页，然后由前端直接发起ajax请求拉取服务器数据。避免在nodejs端执行服务端请求由于服务端后台无法响应造成堵塞导致部分链接被挂起。（nodejs的事件循环和浏览器是不同的，虽然都是基于V8引擎。这也是大部分国内互联网公司在vue-ssr这块的普遍应用方式）