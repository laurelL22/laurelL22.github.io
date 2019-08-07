---
title: ES6-tips
date: 2018-04-04 14:53:33
tags: middlewares
categories:
    - cate
---

## ES6 Tips

### 字符串

1. 带标签的模板字符

```js
let name = 'zhang3';
let age = 9;
function tag(strArr, ...args) {
    var str = '';
    for (let i = 0; i < args.length; i++) {
        str += strArr[i] + args[i]
    }
    console.log(strArr, args);

    str += strArr[strArr.length - 1];
    return str;
}
let str = tag`${name}今年${age}岁了`；
console.info(str);
```

2. includes 方法

判断字符串中是否包含某个字符串

```js
let str = 'xingqitian'；
str.includes('ti'); // true
```



3. endsWidth、startsWith 方法

判断字符串是否以某一个字符串开始或结束

```js
var a = '234567899sddcdhs';
a.startsWith('23');
a.endsWith('Hs');
```



### 函数

1. 函数参数可赋值，可解构

```js
function({a=5}){}
```

2. 箭头函数

- 如果参数只有一个，可省略小括号
- 如果不写return，可以不写大括号
- 没有arguments变量
- 不改变this指向

```js
// 求和
let sum = (a,b)=>a+b;
```

### 数组

1. 数组新方法

- Array.from(); //将类数组转化成数组
- Array.of(); //创建数组
- Array.fill();//填充数组
- Array.reduce();//传入回调适合处理 临近数组元素
- Array.filter();//数组过滤
- Array.find();//查找返回值
- Array.includes();//判断数组是否有某值

```js
Array.of(2,3)   // [2,3]
```

### Class 类

- Class 定义类
- extends 实现继承
- 支持静态方法
- constructor 构造方法