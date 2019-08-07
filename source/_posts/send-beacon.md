---
title: send-beacon
date: 2018-02-07 15:35:40
tags: javascript
---

## canceled request

开发者主动放弃请求的方式：

1. 把一个发请求的dom元素删除掉；
2. 在一个资源加载的时候修改它的请求地址，比如iframe
3. 对同一个服务器的请求太多，导致某一次出现错误

## Navigator.sendBeacon

[sendBeacon](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)
通过HTTP将少量数据异步传输到web服务器。

- 在卸载事件处理器中创建一个图片元素并设置它的src属性的方法来延迟卸载以保证数据的发送。
- 通过创建一个几秒中的noop循环来延迟卸载并向服务器发送数据。

sendBeacon()方法，会使用户代理在有机会时异步地向服务器发送数据，同时不会延迟页面的卸载活影响下一导航的载入性能。

```js
window.addEventListener('unload', logData, false);

function logData() {
    navigator.sendBeacon('/log', data)
}
```