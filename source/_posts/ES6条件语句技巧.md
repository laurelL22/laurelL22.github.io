---
title: ES6条件语句技巧
date: 2018-10-30 14:13:37
tags: ES6
categories:
    - cate
---

### 使用Array.includes 来处理多个条件

```js
function test(val) {
    const arr = ['zhang3', 'li4', 'wang5'];
    if (arr.includes(val)) {
        console.log('got it');
    }
}
```

### 减少嵌套，提前使用 return 语句

```js
// 发现无效条件时提前 return
function test(val) {
    const arr = ['zhang3', 'li4', 'wang5'];
    if (!val) {
        throw new Error('no val'); // return
    }
    if (arr.includes(val)) {
        console.log('got it');
    }
}
```

### 使用函数的默认参数 和 解构

```js
// 默认参数
function test(val, q = 1) {
    if (!val) {
        return;
    }
    console.info(`val is${val} and q id ${q}`);
}

// 解构
function test({name} = {}) {
    console.info(name || 'unknown')
}

// 使用name变量取代val.name 变量
// 还需将{}指定为默认值。否则可能会报错无法解析’undefined’或’null’的属性名称
// 还可以引入loadsh库， _.get()方法
```

### 选择Map/Object 字面量，而不是Switch语句

```js
// 使用对象字面量，根据颜色找出对应的水果
const fruitColor = {
    red: ['apple', 'strawberry'],
    yellow: ['banana', 'pineapple'],
    purple: ['grape', 'plum']
};
function test(color) {
    return fruitColor[color] || [];
}

// 使用 Map ，根据颜色找出对应的水果(Map是新的对象类型，允许存储键值对)
const fruitColor = new Map()
    .set('red', ['apple', 'strawberry'])
    .set('yellow', ['banana', 'pineapple'])
    .set('purple', ['grape', 'plum']);
function test(color) {
    return fruitColor.get(color) || [];
}

// 也可以使用Array.filter 重构
```

### 使用Array.every 和 Array.some来处理全部/部分满足条件


