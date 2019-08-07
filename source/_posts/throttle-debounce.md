---
title: throttle-debounce
date: 2018-01-25 15:37:57
tags: middlewares
categories:
    - cate
---

## throttle & debounce (节流和防抖)

### 节流[throttle]

使用setTimeout方法，给定两个时间，后面的时间减去前面的时间，到达给定的时间就去触发一个这个事件。
在规定的每300毫秒触发一次，当然时间可以自定义，根据需求来；

```js
var scrollBox = document.querySelector('.scroll-box');
    console.info(scrollBox)
    // 调用throttle函数，传入相应的方法和规定时间
    var thro = throttle(throFunc, 300);
    // 触发时间
    scrollBox.on('scroll', function() {
        // 调用此函数
        thro();
    })

    // 封装函数
    function throttle(method, time) {
        var timer = null;
        var startTime = new Date();
        return function() {
            var context = this
            var endTime = new Date();
            var resTime = endTime - startTime
            if(resTime >= time) { // 判断大于等于给定的时间就采取执行函数
                method.call(context)
                // 执行完函数之后重置初始时间，等于最后一次触发的时间
                startTime = endTime;
            }
        }
    }

    // 封装函数
    function debounce(method, time) {
        var timer = null;
        return function() {
            var context = this;
            // 在函数执行的时候先清除timer定时器；
            clearTimeout(timer);
            timer = setTimeout(function() {
                method.call(context);
            },time);
        }
    }

    function throFunc() {
        console.log('success');
    }
```

### 防抖[debounce]

防抖的思想就是当拖动完成之后，两边的窗口位置再重新去计算，这样操作dom结构的次数就大大减少了；
优化了页面性能，降低了内存消耗
在某个事件没有结束之前，函数不会执行，当结束之后，给定延时时间，让他在给定的延时时间之后再去执行这个函数，这就是防抖函数；

### sumary

节流就是每ms执行一次函数，防抖就是最后一次触发后ms后执行一次回调函数。

- 当做keyup像后台请求检验的时候，可以使用防抖函数，不然每按一次键盘就请求一次，请求太频繁，这样当结束按键盘的时候再去请求，请求少很多了，性能自然不用说；
- resize 窗口大小调整的时候，可以采用防抖技术也可以使用节流；
- mousemove 鼠标移动事件既可以采用防抖也可以使用节流；
- scroll 滚动条触发的事件，当然既可以采用防抖也可以采用节流；
- 连续高频发的事件都可以采用这两种方式去解决，优化页面性能；

### demo