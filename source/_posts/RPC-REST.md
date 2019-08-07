---
title: RPC-REST
date: 2019-04-12 10:15:36
tags: server
categories:
    - server
---

### RPC

远程调用框架（Remote Procedure Call），被调用方法的具体实现不在程序运行本地，而是在别的某个远程地方

1. 远程调用原理： A (client) 调用 B (server) 提供的方法
   - 首先A与B之间建立一个TCP连接；
   - 然后A把需要调用的方法名以及方法参数序列化成字节流发送出去；
   - B接受A发送过来的字节流，然后反序列化得到目标方法名，方法参数，接着执行相应的方法调用并把结果返回；
   - A接受远程调用结果,输出结果

**RPC框架就是把这几点细节给封装起来，给用户暴露简单友好的API使用**

1. 好处：

   解耦：当server需要对方法内实现修改时，client完全感知不到，不用做任何变更；这种方式在跨部门，跨公司合作的时候经常用到，并且方法的提供者我们通常称为：服务的暴露

### RESTful API

 RESTful 是目前最流行的 API 设计规范，用于 Web 数据接口的设计

 REST – REpresentational State Transfer：表现层状态转移。资源在网络中以某种表现形式进行状态转移。分解开来：

- Resource：资源，即数据（前面说过网络的核心）。比如 newsfeed，friends等；
- Representational：某种表现形式，比如用JSON，XML，JPEG等；
- State Transfer：状态变化。通过HTTP动词实现。

### URL设计

1. RESTful 的核心思想是客户端发出的数据操作指令都是”动词 + 宾语”的结构

   动词通常就是五种 HTTP 方法，对应 CRUD 操作：

   - GET：读取（Read）
   - POST：新建（Create）
   - PUT：更新（Update）
   - PATCH：更新（Update），通常是部分更新
   - DELETE：删除（Delete）

```
宾语就是 API 的 URL，是 HTTP 动词作用的对象。
```

1. 状态码必须精确

   HTTP状态码，分5个类别：

   - 1xx：相关信息
   - 2xx：操作成功
   - 3xx：重定向
   - 4xx：客户端错误
   - 5xx：服务器错误

2. 服务器回应

   - API 返回的数据格式不应是纯本文，而应该是一个 JSON 对象（Content-Type属性设为application/json）
   - 发生错误时，不要返回 200 状态码：状态码反映发生的错误，具体的错误信息放在数据体里面返回
   - 提供链接：API 的使用者未必知道，URL 是怎么设计的。解决方法就是，在回应中，给出相关链接，便于下一步操作

## RPC与REST有什么区别

RPC是client/server模式的，调用远程的方法，REST也是一套API调用协议方法，它也是基于client/server模式的，调用远程的方法的。

REST API 和 RPC 都是在 Server端 把一个个函数封装成接口暴露出去，以供 Client端 调用，但REST API 是基于HTTP协议的，REST致力于通过http协议中的POST/GET/PUT/DELETE等方法和一个可读性强的URL来提供一个http请求。而 RPC 则可以不基于 HTTP协议
因此，如果是后端两种语言互相调用，用 RPC 可以获得更好的性能（省去了 HTTP 报头等一系列东西），应该也更容易配置

Link:

- [http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html](http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)
- https://blog.csdn.net/cs958903980/article/details/78674087