---
title: require.context
date: 2020-04-21 10:43:54
tags: es6
categories:
    - cate-es6
---

## require.context自动导入模块

### 概述
通过 require.context() 函数来创建自己的 context。

给这个函数传入三个参数：一个要搜索的目录，一个标记表示是否还搜索其子目录， 以及一个匹配文件的正则表达式。

webpack 会在构建中解析代码中的 require.context()


` require.context(directory, useSubdirectories = false, regExp = /^\.\//); `

### 示例

```js
// （创建出）一个 context，其中文件来自 test 目录，request 以 `.test.js` 结尾。

require.context('./test', false, /\.test\.js$/);

```

```js
/**
* inject components 自动化导入组件
*/
const files = require.context('.', true, /index\.vue$/)

let components = {};
function upperCased(str) {
    return str.replace(/-([a-z])/g, (s, p) => p.toUpperCase()).replace(/^\S/, s => s.toUpperCase());
}

files.keys().forEach(key => {
    const componentEntity = files(key).default
    components[upperCased(componentEntity.name)] = componentEntity // 读取出文件中的default模块
})
export default components

```