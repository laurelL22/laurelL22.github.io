---
title: JS新操作符
date: 2019-12-19 19:38:29
tags:
---

### 1、Optional chaining operator(可选链操作符)

可选链操作符?.能够去读取一个被连接对象的深层次的属性的值而无需明确校验链条上每一个引用的有效性。?.运算符功能类似于.运算符，不同之处在于如果链条上的一个引用是nullish (null 或 undefined)，.操作符会引起一个错误，?.操作符取而代之的是会按照短路计算的方式返回一个undefined。
当访问链条上可能存在的属性却不存在时，?.操作符将会使表达式更短和更简单。

语法：

- obj?.prop
- obj?.[expr]
- arr?.[index]
- func?.(args)

```js
// eg1:
const adventurer = {
    name: 'zhang3',
    cat: {
        name: 'li4'
    }
};

const dogName = adventurer.dog?.name;
console.log(dogName); // expected output: undefined

console.log(adventurer.someNonExistentMethod?.()) // expected output: undefined


// eg2:函数调用时如果被调用的方法不存在，使用可选链可以使表达式自动返回undefined而不是抛出一个异常。
let result = someInterface.customMethod?.();


// eg3: 在使用可选链时设置一个默认值
let customer = {
  name: "Carl",
  details: { age: 82 }
};
let customerCity = customer?.city ?? "Unknown city";
console.log(customerCity); // Unknown city
```


### 2、Nullish coalescing operator 空值合并运算符
空值合并运算符（??）是一个逻辑运算符。当左侧操作数为 null 或 undefined 时，其返回右侧的表达式。否则返回左侧的操作数。

> 直接与 AND（&&）和 OR（||）操作符组合使用 ?? 是不可取的

```js
const foo = null ?? 'default string';
console.log(foo); // expected output: "default string"

const baz = 0 ?? 42;
console.log(baz); // expected output: 0

null || undefined ?? "foo"; // raises a SyntaxError
(null || undefined ) ?? "foo"; // returns "foo"

```

### 3、Pipeline operator管道操作符 |>
管道操作符是单参数函数调用的语法糖.
- 函数链式调用,使用管道操作符可以改善代码的可读性
```js
const double = (n) => n * 2;
const increment = (n) => n + 1;

// 没有用管道操作符
double(increment(double(5))); // 22

// 用上管道操作符之后
5 |> double |> increment |> double; // 22
```

### 其他
- delete 操作符：
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete
- 逗号操作符：
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comma_Operator