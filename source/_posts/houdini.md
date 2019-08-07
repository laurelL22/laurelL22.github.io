---
title: houdini
date: 2017-11-23 15:50:45
tags: css
categories:
    - cate
---

## CSS Houdini

> Houdini 是 W3C 新成立的一个任务小组，它的终极目标是实现 css 属性的完全兼容

> CSS Houdini — W3C提出关于CSS新的草案

------

### 关于草案

- 关于CSS的每一份草案都是经过了许多年才被广泛使用；
- 一个新特性被写入CSS标准需要经过一整套标准指定流程，需要很长的时间；
- 最新的浏览器也会和新属性提案仍然存在一个时间差；

| 1                   | 2                          | 3              |
| :------------------ | :------------------------- | :------------- |
| 新特性—（厂商需求） | 一些浏览器支持（厂商工期） | 绝大浏览器支持 |

------

### JS 社区和CSS社区

- 在 JS 社区里，你总能听到人们在抱怨它一天一个样 －－ 跑得太快追得太累。。
- 而 CSS 社区，学习新事物吃力 －－ 天知道什么时候才能用上。。。

- CSS in js — 如react
- JS in CSS — Houdini
  - Parsing — > Rendering （很多草案集合），支持不是很好
  - css不能触摸到运行时，比如不能实现varibles（预编译和运行时动态修改CSS值）

> JavaScript 是一种动态语言，用 JavaScript 给 JavaScript 打补丁是可以轻松实现的，但 CSS 不是动态的。
> 在某些情况下，可以在构建过程中将一种形式的 CSS 转译成另一种形式（PostCSS ）。
> 而如果补丁是依赖于的 DOM 结构、某一个元素的布局或者它的定位，那补丁程序就需要在本地运行了。

------

### 是否可以控制浏览器解析 HTML 和 CSS 的过程？

不能控制浏览器解析 HTML 和 CSS 的过程，只能看它生成 DOM 和 CSS object model (CSSOM)。

- 没法控制级联（cascade）；
- 没法控制浏览器布局元素的方式（layout）；
- 没法控制元素在屏幕上显示的过程（paint）；
- 合成（composite）也不能控制。

[![css-parser](http://laurell22.github.io/images/css-parser.png)](http://laurell22.github.io/images/css-parser.png)

```
(HTML 文档从被浏览器接收到显示在屏幕上的全过程)
```

整个过程中，能完全控制的唯一环节就是 DOM，另外 CSSOM 也可以部分控制到。
but这种程度的暴露是“不确定的、兼容性不稳定的以及缺乏对关键特性的支持的”。

浏览器中的 CSSOM 是不会告诉你它是如何处理跨源的样式表的，而且对于浏览器无法解析的 CSS 语句它的处理方式就是不解析了

假设要用 CSS polyfill 让浏览器去支持它尚且不支持的属性，那就不能在 CSSOM 这个环节做，只能自行解析一遍 DOM 树，找到`<style> 和 <link rel="stylesheet">` 标签，获取其中的 CSS 样式、解析、重写，最后再加回 DOM 树中。DOM 树刷新，渲染页面步骤会重新走一遍。

[![broswer-parser-01](http://laurell22.github.io/images/broswer-parser-01.png)](http://laurell22.github.io/images/broswer-parser-01.png)

```
（JavaScript Polyfills 只能去更改 DOM 和 CSSOM，大部分这样的操作，都会导致页面重新渲染。）
```

------

### 重渲染过程频繁地发生，如果polyfill 是需要应对页面上的所有交互呢？

scroll 事件、窗口缩放、鼠标移动还有键盘输入……都会被触发的重渲染会把页面拖得很慢。
大部分的CSS pollyfills都有各自的解析器和层级逻辑，而且”解析器”和”层级逻辑”非常复杂，导致这些polyfills文件大或者有太多bug

如果想要浏览器做出它本来做不到事情（比如让它解析你给的样式，不管它能不能实现该样式），而渲染流程里你无法插手其他步骤，所以只能通过手动更新和改变 DOM 的方式。



**“干涉”浏览器解析样式的目的：（更改浏览器内部的渲染引擎）**

- to normalize cross-browser differences(统一跨浏览器行为)
- to invent or polyfill new features so that people can use them today(开发新特性或者给新特性打补丁，让人们可以立刻使用到新特性。)

**CSS新特性**

- CSS Flexible box
- CSS Grid （刚刚完成支持）
- CSS Position sticky

**CSS 新特性很难做到真正意义上的polyfill**

**Babel 是JS在生产环境可用的polypill方案**

**houdini 小组就想要实现实现 css 属性的完全兼容，每个样式都能在保证性能的前提下使用**

------

### 新规范融入浏览器渲染过程的部分

[![new-rule-in-broswer](http://laurell22.github.io/images/new-rule-in-broswer.png)](http://laurell22.github.io/images/new-rule-in-broswer.png)

CSS小组在解决干涉浏览器渲染过程的问题上，引入了一些新的标准。开发者可以用这些标准来控制对应的环节。

------

### 新规范：

1. CSS Parsing API

   允许开发者自由扩展 CSS 词法分析器，引入新的结构（constructs）

2. CSS 属性与值 API

```css
body {
    --primary-theme-color: tomato;
    transition: --primary-theme-color 1s ease-in-out;
}
body.night-theme {
    --primary-theme-color: darked;
}
```

可以在 cascade 步骤结束时，通过一个钩子更改一个元素的自定义属性值，这个特性对于 polyfills 开发很有意义

1. CSS Typed OM

目的是解决目前模型的一些问题，并实现 CSS Parsing API 和 CSS 属性与值 API 相关的特性。
将 CSSOM 的字符串值转成有意义的 JS 表达式可以有效的提升性能。将CSS值暴露出来，也可以自定义

**CSS Typed OM ====> CSSOM + Typed OM — > Typed Value = Value + Type(30 + ‘px’)**

1. CSS Layout API

可以通过 CSS Layout API 实现自己的布局模块（layout module），“布局模块”指的是 display 属性值。

1. CSS Paint API

CSS Paint API 和 Layout API 非常相似。它提供了一个 registerPaint 方法，操作方式和 registerLayout 方法也很相似。假设要构建一个 CSS 图像的时候，可以调用 paint() 函数，也可以使用刚刚注册好的名字。

[![css-paint-api](http://laurell22.github.io/images/css-paint-api.png)](http://laurell22.github.io/images/css-paint-api.png)
在 CSS 中可以这样使用它（这个圆形背景将始终占满 .bubble 元素）：

```css
.bubble {
    --circle-color: blue;
    background-image: paint('circle');
}
```



1. Worklets
2. 复合滚动和动画

------

### 参考

- wiki：https://github.com/w3c/css-houdini-drafts/wiki
- 视频地址： [http://www.itdks.com/eventlist/detail/1627](http://www.itdks.com/eventlist/detail/1627)
- demo：https://github.com/iamvdo/houdini-experiments