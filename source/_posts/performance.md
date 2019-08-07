---
title: performance
date: 2018-03-22 15:26:18
tags: middlewares
categories:
    - cate
---

## performance

[链接](https://segmentfault.com/a/1190000004466407)

### performance API

[performace API](http://javascript.ruanyifeng.com/bom/performance.html)

用于精确度量、控制、增强浏览器的性能表现。

- performance.timing
  - performance.timing.navigationStart 浏览器处理当前网页的启动时间

```js
var t = performance.timing;

// 页面加载的耗时
var pageloadtime = t.loadEventStart - t.navigationStart;

// 域名解析的耗时
var dns = t.domainLookupEnd - t.domainLookupStart;

// tcp 连接的时间
var tcp = t.connectEnd - t.connectStart;

var ttfb = t.responseStart - t.navigationStart;
```

- performance.navigation 对象
  - performance.navigation.type：网页加载的来源
  - performance.navigation.redirectCount：表示当前网页经过了多少次重定向跳转

### performance 对象属性

```js
var performance = {
    memory: {
        usedJSHeapSize, // JS对象占用的内存，一定小于totalJSHeapSize    
        totalJSHeapSize, // 可使用的内存
        jsHeapSizeLimit, // 内存大小限制
    },
    navigation: {
        redirectCount: 0, // 如果有重定向，页面通过几次重定向跳转而来
        type: 0,    // 0    即 TYPE_NAVIGATENEXT 正常进入的页面（非刷新、非重定向等）
                    // 1    即 TYPE_RELOAD 通过window.location.reload()刷新的页面
                    // 2    即 TYPE_BACK_FORWARD 通过浏览器的前进后退按钮进入的页面（历史记录）
                    // 225    即 TYPE_UNDEFINED 非以上方式进入的页面
    },
    timing: {
        // 在同一个浏览器上下文中，前一个网页（与当前页面不一定同域）unload 的时间戳，如果无前一个网页 unload ，则与 fetchStart 值相等
        navigationStart: 1441112691935,

        // 前一个网页（与当前页面同域）unload 的时间戳，如果无前一个网页 unload 或者前一个网页与当前页面不同域，则值为 0
        unloadEventStart: 0,

        // 和 unloadEventStart 相对应，返回前一个网页 unload 事件绑定的回调函数执行完毕的时间戳
        unloadEventEnd: 0,

        // 第一个 HTTP 重定向发生时的时间。有跳转且是同域名内的重定向才算，否则值为 0 
        redirectStart: 0,

        // 最后一个 HTTP 重定向完成时的时间。有跳转且是同域名内部的重定向才算，否则值为 0 
        redirectEnd: 0,

        // 浏览器准备好使用 HTTP 请求抓取文档的时间，这发生在检查本地缓存之前
        fetchStart: 1441112692155,

        // DNS 域名查询开始的时间，如果使用了本地缓存（即无 DNS 查询）或持久连接，则与 fetchStart 值相等
        domainLookupStart: 1441112692155,

        // DNS 域名查询完成的时间，如果使用了本地缓存（即无 DNS 查询）或持久连接，则与 fetchStart 值相等
        domainLookupEnd: 1441112692155,

        // HTTP（TCP） 开始建立连接的时间，如果是持久连接，则与 fetchStart 值相等
        // 注意如果在传输层发生了错误且重新建立连接，则这里显示的是新建立的连接开始的时间
        connectStart: 1441112692155,

        // HTTP（TCP） 完成建立连接的时间（完成握手），如果是持久连接，则与 fetchStart 值相等
        // 注意如果在传输层发生了错误且重新建立连接，则这里显示的是新建立的连接完成的时间
        // 注意这里握手结束，包括安全连接建立完成、SOCKS 授权通过
        connectEnd: 1441112692155,

        // HTTPS 连接开始的时间，如果不是安全连接，则值为 0
        secureConnectionStart: 0,

        // HTTP 请求读取真实文档开始的时间（完成建立连接），包括从本地读取缓存
        // 连接错误重连时，这里显示的也是新建立连接的时间
        requestStart: 1441112692158,

        // HTTP 开始接收响应的时间（获取到第一个字节），包括从本地读取缓存
        responseStart: 1441112692686,

        // HTTP 响应全部接收完成的时间（获取到最后一个字节），包括从本地读取缓存
        responseEnd: 1441112692687,

        // 开始解析渲染 DOM 树的时间，此时 Document.readyState 变为 loading，并将抛出 readystatechange 相关事件
        domLoading: 1441112692690,

        // 完成解析 DOM 树的时间，Document.readyState 变为 interactive，并将抛出 readystatechange 相关事件
        // 注意只是 DOM 树解析完成，这时候并没有开始加载网页内的资源
        domInteractive: 1441112693093,

        // DOM 解析完成后，网页内资源加载开始的时间
        // 在 DOMContentLoaded 事件抛出前发生
        domContentLoadedEventStart: 1441112693093,

        // DOM 解析完成后，网页内资源加载完成的时间（如 JS 脚本加载执行完毕）
        domContentLoadedEventEnd: 1441112693101,

        // DOM 树解析完成，且资源也准备就绪的时间，Document.readyState 变为 complete，并将抛出 readystatechange 相关事件
        domComplete: 1441112693214,

        // load 事件发送给文档，也即 load 回调函数开始执行的时间
        // 注意如果没有绑定 load 事件，值为 0
        loadEventStart: 1441112693214,

        // load 事件的回调函数执行完毕的时间
        loadEventEnd: 1441112693215
    }
}
```

### performance.now()

```js
performance.now();  // 输出是微秒级别
Date.now();         // 输出是毫秒级别
```

- Date.now() 输出从1970年开始的毫秒数
- performance.now() 从performance.timing.navigationStart(页面开始加载)的时间，到当前时间的微妙数。

```js
var timesnipe = performance.now();
    document.addEventListener('DOMContentLoaded', function() {
        console.log(performance.now() - timesnipe);
    }, false);

    window.addEventListener('load', function() {
        console.log(performance.now() - timesnipe);
    }, false);
    // 这样并不等同于，只能算约等于
    performance.timing.domContentLoadedEvenntStart - performance.timing.domLoading; // 检测domLoadEvent触发时间
```

### redirect 重定向，页面加载的第一步

- 当一个页面迁移，但是你输入原来的网址就会发生重定向，这样就会经过两次DNS解析，TCP连接，以及请求的发送。

- 使用.performance 的属性，计算出重定向的时间

  ```js
  redirectTime = redirectEnd - redirectStart
  ```

### cache,DNS, TCP, Request, Response

- 输入的域名正确，浏览器会查询本地是否有域名缓存（appCache）
- 如果有，则不需要进行DNS解析，否则需要对域名进行解析，找到真正的IP地址
- 建立3次握手连接
- 发送请求
- 接受数据

这部分可以做的优化有：

1. 发送请求的优化:加异地机房，加CDN（加快解析request）
2. 请求加载数据的优化：页面内容经过gzip压缩，静态资源css/js等压缩（request到response的优化）

**performance的相关操作，最好放在onload的回调中执行，避免出现异常的bug**

```js
// DNS 缓存时间
    times.apache = t.domainLookupStart - t.fetchStart;
// TCP 建立连接完成握手的时间
    times.connect = t.connectEnd - t.connectStart;
// DNS 查询时间
    times.lookupDomain = t.domainLookupEnd - t.domainLookupStart;
// 整个解析时间
    var lookup = t.responseEnd - t.responseStart;
```



### process,onload

- 解析HTML结构
- 加载外部脚本和样式表文件
- 解析并执行脚本代码
- 构造HTML DOM模型。 // ready 执行
- 加载图片等外部文件
- 页面加载完毕 // load 执行

```js
// 计算DOMContentLoaded 触发时间
var contentLoadedTime = t.domContentLoadedEventStart - t.domLoading
// 计算load触发时间
var loadTime = t.domComplete - t.domLoading;
```

如果js文件设计DOM操作，可以直接在DOMContentLoaded里面添加回调函数，或者说基本上js文件都可以在写在里面进行调用。这和将js文件放在body底部，在js上面加async，defer，以及hard Callback异步加载js文件的效果一样。

------

## jquery ready 事件解析

### readyStateChange

这是IE6，7，8的特有属性，用它来标识某个元素的加载状态。但w3c规定，只有xhr才有这个事件。一般只能在IE中使用readyStateChange。否则，其他浏览器是没有效果的。

### doScroll 兼容

这是IE低版本特有的，IE11已经弃用了。使用scrollLeft和scrollTop代替。

doScroll的主要作用是检测DOM结构是否有问题，通常会使用轮训来检测doScroll是否可用，当可用时一定是DOM结构稳定，图片资源还未加载的时候。

------

## JS 中运行时间函数console.time()

需要在调试过程中知道代码执行的时间，可以通过在JS中添加console.time()和console.timeEnd()来对程序的执行进行计时

```js
function foo() {
    var x = 100, y = 300;
    return x * y;
}

// 计时
console.time('test');

foo();

console.timeEnd('test');
```

console.time()和console.timeEnd()接收一个字符串作为参数，字符串相当于计时的id。浏览器会将拥有相同参数(id)的console.time()与console.timeEnd()进行配对，记录两者之间的时间差

### 浏览器兼容性

- ff > 10.0
  - 之前的版本可通过安装Firebug插件实现
- chrone > 2.0
- IE > IE 11
  - 之前版本的IE，可通过fireBug Lite实现