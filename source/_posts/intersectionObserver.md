---
title: intersectionObserver
date: 2018-01-24 15:48:31
tags: middlewares
categories:
    - cate
---

## IntersectionObserver API 交叉观察器

可以自动观察元素是否在视口内。可见（visible）的本质：目标元素与视口产生一个交叉区

### 用法

- 参数：
  - callback, 可见性变化时的回调函数
  - option, 配置对象（可选）
- 返回值：观察器实例
- 方法：
  - observe() 方法可以指定观察哪个 DOM 节点。(参数是一个DOM节点对象)

```js
var io = new IntersectionObserver(callback, option);
```

### callback参数

目标元素的可见性变化时，就会调用

callback一般会触发两次。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）。

```js
var io = new IntersectionObserver(
    entries => {
        console.log(entries);
    }
);
```

callback函数的参数（entries）是一个数组，每个成员都是一个IntersectionObserverEntry对象。举例来说，如果同时有两个被观察的对象的可见性发生变化，entries数组就会有两个成员。

### IntersectionObserverEntry 对象

提供目标元素的信息，一共有六个属性：

- time
- rootBounds
- boundingClientRect
- intersectionRect
- intersectionRatio
- target

### Demo

```js
function checkImgs() {
    const imgs = Array.from(document.querySelectorAll('.my-photo'));
    console.log(imgs)
    imgs.forEach(item => io.observe(item));
}

function loadImg(el) {
    if (!el.src) {
        const source = el.dataset.src;
        el.src = source;
    }
}

const io = new IntersectionObserver(ioes => {
    console.info(ioes[0]);
    ioes.forEach(ioe => {
        const el = ioe.target;
        const intersectionRatio = ioe.intersectionRatio;
        if (intersectionRatio > 0 && intersectionRatio <= 1) {
            loadImg(el);
        }
        el.onload = el.onerror = () => io.unobserve(el);
    })
})
```

### 说明

IntersectionObserver API 是异步的，不随着目标元素的滚动同步触发。

规格写明，IntersectionObserver的实现，应该采用requestIdleCallback()，即只有线程空闲下来，才会执行观察器。这意味着，这个观察器的优先级非常低，只在其他任务执行完，浏览器有了空闲才会执行。

[link](http://www.ruanyifeng.com/blog/2016/11/intersectionobserver_api.html)