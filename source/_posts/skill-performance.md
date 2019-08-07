---
title: skill-performance
date: 2018-03-23 15:15:55
tags: javascript
---

## js技巧

### 1、new Set()

`
利用Set数据结构能够去重一个数组
`
```js
let arr = [1,2,2,3];
let set = new Set(arr);
let newArr = Array.from(set); // Array.from方法可以将Set结构转为数组
console.log(newArr); // [1,2,3]
```



### 2、Object.assign()

`Object.assign()也是ES6中提供的对象的扩展方法，可以用于对象的合并拷贝`

```js
let obj1 = {a: 1};
let obj2 = {b: 2};
let obj3 = Object.assign({}, obj1, obj2);
console.log(obj3) // {a: 1, b:2}
```



### 3、map()

`map方法用于遍历数组，有返回值，可以对数组的每一项进行操作并生成一个新的数组，有些时候可以代替for和forEach循环，简化代码`
```js
let arr3 = [1,2,3,4,5];
let newArr3 = arr3.map((e, i) => e*10 );
console.log(newArr3)
```



### 4、filter()

filter方法用于遍历数组，就是过滤数组，在每一项元素后面触发一个回调函数，通过判断，保留或移出当前项，最后返回一个新的数组

```js
let arr4 = [1,2,3,4,5];
let newArr4 = arr4.filter((e, i) => e%2 === 0); // 取模，过滤余数不为0的数
```



### 5、some()

some方法用于遍历数组，在每一项元素后面触发一个回调函数，只要一个满足条件就返回true,否则就返回false。类似于||比较

```js
let arr5 = [{result:true}, {result: false}];
let newArr5 = arr.some((e, i) => e.result); //只要一个为true,即为true
console.log(newArr5); // true
```

### 6、every()

every方法用于遍历数组，在每一项元素后面触发一个回调函数，只要一个不满足条件就返回false,否则返回true,类似于&&比较

```js
let arr6 = [{result:true}, {result: false}];
let newArr6 = arr.some((e, i) => e.result); //只要一个为true,即为true
console.log(newArr6); // false
```



### 7、~~运算符

`~符号在JS中有按位取反的作用，~~ 即是取反两次，而位运算的操作值要求是整数，其结果也是整数，所以经过位运算的都会自动变成整数，可以巧妙的去掉小数部分，类似于parseInt()`

```js
let a = 1.23;
let b = -1.23;

console.log(~~a); // 1
console.log(~~b); // -1
```



### 8、|| 运算符

使用||运算符可以给变量设置默认值

```js
let c = 1;
let d = c || 2;  // 如果c的值为true则取存在的值，否则为2

console.log(d);  // 1
```



### 9、…运算符

…运算符是ES6中用于结构数组的方法，可以用于快速获取数组的参数

```js
let [num1, ...nums] = [1,2,3];

console.log(num1); // 1
console.log(num3); // [2,3]
```



### 10、三元运算符

e ? f = ‘man’ : f = ‘woman’

-----------------

## js性能优化小结

### 1、谨慎使用闭包

【由于闭包[[Scope]] 属性包含与执行环境作用域链相同的对象引用，函数活动对象本来会随着执行环境完毕后一同销毁，但引入闭包，对象无法被销毁。】
闭包会造成更多的内存开销，同时还会造成内存泄露

### 2、缓存对象成员

在同一个函数中， 如果存在多次读取同一个对象成员，可以在局部函数中保存对象，减少查找

```js
function getWindowWH() {
    var elBody = document.getElementByTagName('body')[0];
    return {
        width: elBody.offsetWidth,
        height: elBody.offsetHeight
    }
}
```



同时也解决了属性越深，访问速度越慢的问题

### 3、DOM操作

访问DOM的次数越多，代码运行的速度越慢，统一的保存结果最后在一并输出

```js
function innerHTMLLoop() {
    var content = '';

    for(var count = 0; count < 1000; count++) {
        content += 'a'
    }
    document.getElementById('idName').innerHTML += content;
}
```



### 4、重绘（repaints）与重排（reflows）

当页面布局和几何属性改变时就需要”重排”
避免在修改样式的过程中使用offsetTop, scrollTop, clientTop, getComputedStyle()这些属性，它们都会重新渲染队列
最小化重绘和重排，尽量一次处理

1）使元素脱离文档流

2）使用documentFragment

3）将原始元素拷贝到一个脱离文档流的节点中，修改副本，完成后再替换元素元素

[link](https://www.jianshu.com/p/b8f7e6c32a1b)

#### reflow

reflow（回流）是导致DOM脚本执行效率低的关键因素之一，页面上任何一个节点触发了reflow，会导致它的子节点及祖先节点重新渲染。

**导致reflow发生**

- 改变窗口大小
- 改变文字大小
- 添加/删除样式表
- 内容的改变，（用户在输入框中写入内容也会）
- 激活伪类，如:hover
- 操作class属性
- 脚本操作DOM
- 计算offsetWidth和offsetHeight
- 设置style属性

#### repaint

repaint是在一个元素的外观被改变，但没有改变布局的情况下发生的，如改变了visibility、outline、background等。

### 5、事件委托

当有很多元素需要绑定事件的时候，我们一个一个的去绑定事件是有代价的的，元素越多应用程序越慢。事件绑定不但占用了处理时间，并且追踪事件需要更多的内存，有时候很多元素是不需要，或者是用户不会点击的，所以我们需要使用事件委托来解决没有必要的资源消耗。

例子： 我们需监听li的click事件，通过冒泡事件来获取点击的对象。



```html
<ul>
    <li index='1'>1</li>
    <li index='2'>2</li>
    <li index='3'>3</li>
</ul>
```



------

```js
var ul = document.getElementById('ul');

ul.addEventListener('click', function(e) {
  var now_index = e.target.getAttribute('index');
  ...
})
```

### 6、循环性能

一般的for语句可能很多人都这么写

```js
for(var i = 0; i < array.length; i++){
    ....
}
```

每次循环的时候需要查找array.length，这样不但很耗时，也造成性能损失。只要查找一次属性，存储在局部变量，就可以提高性能。

```js
for(var i = 0, len = array.length; i < len; i++){
    ....
}
```

重写后的循环根据数组的长度能优化25%的运行时间，IE更多。所以平时书写的时候还是要多加注意。同时还是要避免使用for-in循环。

### 7、条件语句

if-else 对比 switch， 当条件语句较多的时候switch 更易读，运行的要更快。所以但条件少的情况下使用if-else，当条件增加时，更倾向于switch，会更佳合理。

### 8、避免使用构造器

通过避免使用eval()和Function()构造器来避免双重求值带来的性能消耗

eval(‘2 + 2’)

### 9、使用 Object/Array 直接量

直接量的速度会更快。

```js
//bad
var myObject = new Object();
myObject.name = "xxxx";

//good
var myObject = {
    name: "xxxx"
}
```