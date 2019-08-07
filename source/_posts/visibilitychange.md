---
title: visibilitychange
date: 2018-01-25 13:43:48
tags: javascript
categories:
    - cate
---

## Page Visibility API 页面可见性

[Page Visibility api](https://developer.mozilla.org/zh-CN/docs/Web/API/Page_Visibility_API)

### Demo

```js
/* 各种浏览器兼容(在页面可见性变化时修改document.title) */
var hidden, state, visibilityChange
if (typeof document.hidden !== 'undefined') {
    hidden = 'hidden';
    visibilityChange = 'visibilitychange';
    state = 'visibilityState'
} else if (typeof document.mozHidden !== 'undefined') {
    hidden = 'mozHidden';
    visibilityChange = 'mozvisibilitychange'
    state = 'mozVisibilityState'
} else if (typeof document.msHidden !== 'undefined') {
    hidden = 'msHidden';
    visibilityChange = 'msvisibilitychange'
    state = 'msVisibilityState'
} else if (typeof document.webkitHidden !== 'undefined') {
    hidden = 'webKitHidden';
    visibilityChange = 'webkitvisibilitychange'
    state = 'webkitVisibilityState'
}
var n = 0
// 添加监听器，在title里显示状态变化
document.addEventListener(visibilityChange, function() {
    ++n
    document.title = document[state] + ':' +n
    document.getElementById('num').innerText = n
}, false)

// 初始化
document.title = document[state] + ':' +n;
document.getElementById('num').innerText = 0
```