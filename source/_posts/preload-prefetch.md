---
title: preload_prefetch
date: 2018-02-13 15:33:54
tags: key
categories:
    - cate
---

## preload 预加载资源

preload让浏览器提前加载指定资源（加载后并不执行），在需要执行时再执行：

- 将加载和执行分离开，可不阻塞渲染和document的onload事件
- 提前加载指定资源，不再出现依赖的font字体隔了一段时间才刷出

### 使用

- 使用link标签创建

  ```html
  <link rel="preload" href="/path/to/style.css" as="style">
  ```

- 使用HTTP响应头的Link字段创建

### 判断浏览器是否支持preload

```js
const isPreloadSupported = () => {
    const link = document.createElement('link');
    const relList = link.relList;

    if (!relList || !relList.supports) {
        return false;
    }

    return relList.supports('preload');
}
```

### 区分preload 和 prefetch

- preload 告诉浏览器页面必定需要的资源，浏览器一定会加载这些资源；
- prefetch 告诉浏览器页面可能需要的资源，浏览器不一定会加载这些资源。

preload是确认会加载指定资源；

prefetch是预测会加载指定资源。