---
title: interview
date: 2019-04-16 11:06:36
tags:

---

## interview

#### HTML

- 不采用缓存的目的是？如何实现？
  - 浏览器有一个临时的存储网站文件的缓存，所以他们不需要在切换或重新加载同一个页面时再次重新下载。服务器设置发送头信息告诉浏览器在给定的一段时间内使用存储文件。可极大加快网站的速度和节省带宽。
  - 当网站更新时，因为用户的缓存依然指向旧文件，会造成问题。如果缓存的CSS和JS文件引用的元素不再存在，已移除或已重命名时，它会保留原有功能或破坏网站
  - 禁用缓存是一个强制浏览器下载新文件的过程。通过命名来区分旧文件
  - 常用的强制浏览器重新下载文件的技术是在文件的结尾处增加一个查询字符串：src=”js/script.js” => src=”js/script.js?v=2”

#### Javascript

- JS 的数据类型

  - 最新的ECMAScript定义了7种数据类型：
    - 原始数据类型：Boolean，Null，Undefined，Number，String，Symbol
    - 引用数据类型：Object [Array, Date, Function, RegExp]

- 避免回调地狱

  - 使用async/await重构函数返回promise是最佳选择；返回一个promise，等待数据接受后然后解析，允许下一行代码以同步风格的方式进行。
  - 模块化：把回调分割为独立函数
  - 使用一个控制流的库，像async
  - 使用一个带有promise的生成器
  - 使用async/await（ES8及以上）

- 什么是Promise

  Promise对象代表的是异步操作最终的完成(或失败)，和它的结果值。Promise是链式调用的。

  - Promise的状态只能是其中之一：
    - pending：初始状态，不是完成态(fulfilled)也不是拒绝态(rejected)
    - fulfilled：意味操作已经完成
    - rejected：意味操作已经失败

- 什么是回调

  回调函数是作为一个参数传递给另一个函数的函数，只有在事件触发或具体任务完成时调用，经常用在异步代码中。回调函数会在一段代码之后调用，但可以在初始化声明，而不需要调用

  回调函数也可以是同步的。

- JS中，如何比较两个对象

即使两个相同属性相同值的不同对象，当使用 == 或 === 进行比较时，也不认为他们相等。因为他们都是通过引用比较的，不像基本类型是通过值比较的。

提供一个辅助函数，它会迭代每个对象每个自己的属性，测试他们是否有相同的值

``` javascript
function isDeepEqual(obj1, obj2, testPrototype = false) {
    if (obj1 === obj2) {
        return true
    }

    if (typeof obj1 === 'function' && typeof obj2 === 'function') {
        return obj1.toString() === obj2.toString()
    }

    if (obj1 instanceof Date && obj2 instanceof Date) {
        return obj1.getTime() === obj2.getTime()
    }

    if (
        Object.prototype.toString.call(obj1) !== Object.prototype.toString.call(obj2)
        && obj1 !== 'object'
    ) {
        return false
    }

    const prototypeAreEqual = testPrototype
        ? isDeepEqual(
            Object.getPrototypeOf(obj1),
            Object.getPrototypeOf(obj2),
            true
        )
        : true


    const obj1Props = Object.getOwnPropertyNames(obj1)
    const obj2Props = Object.getOwnPropertyNames(obj2)

    return (
        obj1Props.length === obj2Props.length &&
        prototypesAreEqual &&
        obj1Props.every(prop => isDeepEqual(obj1[prop], obj2[prop]))
    )
}
```