---
title: JSCode-Test
date: 2019-04-02 11:38:01
tags: code
categories:
    - interview
---

### 异步队列类

```js
(function () {
    TaskQe = function () {
        this._arrayFn = [];  // 事件集合 
        this._callback = {}; // 事件回调
        this._backdata = []; // 返回数据集合
        this._isParallel = false; // 是否并行，默认否 
    }
    
    // 加入队列
    TaskQe.prototype.add = function (fn) {
        this._arrayFn.push(fn);
        return this;
    };
    
    // 队列数目
    TaskQe.prototype.size = function () {
        return this._arrayFn.length;
    }
    
    // 获取返回数据集合
    TaskQe.prototype.getCallBack = function () {
        return this._backdata;
    }
    
    // 依次运行队列
    TaskQe.prototype.next = function (data) {
        var fn = this.getNext()；
        
        // 并行 or 串行
        (this._isParallel)
            ? ((fn) &&fn())
        	: ((fn) && ((data) ? fn(data): fn()))
        
        // 集合返回数据
        (data) ? this._backdata.push(data) : this._backdata.push(false);
        
        // 返回总回调
        if (!this.size() && !fn) {
            if (typeof this._callback === "function") {
                (data) ? this._callback(data) : this._callback();
            }
        }
        
        return this;
    }
    
    // 队列执行
    TaskQe.prototype.getNext = function () {
        if (this.size()) {
            return this.arrayFn.shift();
        }
        return false;
    }
    
    // 初始化，完成后执行，第一个参数是否执行，第二个参数返回
    TaskQe.prototype.run = function () {
        if (arguments.length === 1) {
            this._callback = (typeof arguments[0] === "function") ? arguments[0] : {};
            this._isParallel = (typeof arguments[0] === "boolean") ? arguments[0] :false;
        }
        if (arguments.length === 2) {
            this._callback = (typeof arguments[1] === "function") ? arguments[1] : {};
            this._isParallel = (typeof arguments[0] === "boolean") ? arguments[0] :false;
        }
        
        var firstFn = this.getNext();
        firstFn();
        return this;
    }
    
    return TaskQe;
})();
```

### 实现一个Event类

```js
// 实现一个event类
class EventEmitter{
    constructor(){
        this._events={}
    }
    on(event,callback){
        let callbacks = this._events[event] || []
        callbacks.push(callback)
        this._events[event] = callbacks
        return this
    }
    off(event,callback){
        let callbacks = this._events[event]
        this._events[event] = callbacks && callbacks.filter(fn => fn !== callback)
        return this
    }
    emit(...args){
        const event = args[0]
        const params = [].slice.call(args,1)
        const callbacks = this._events[event]
        callbacks.forEach(fn => fn.apply(this, params))
        return this
    }
    once(event,callback){
        let wrapFunc = (...args) => {
            callback.apply(this,args)
            this.off(event,wrapFunc)
        }
        this.on(event,wrapFunc)
        return this
    }
}


// 测试
let ee = new EventEmitter();
function a() {
    console.log('a')
}
function b() {
    console.log('b')
}
function c() {
    console.log('c')
}
function d(...a) {
    console.log('d',...a)
}
ee.on('TEST1', a).on('TEST2', b).once('TEST2', c).on('TEST2',d);


ee.emit('TEST1');
console.log('....')
ee.emit('TEST2');
// In test2
// In test2 again
console.log('....')
ee.emit('TEST2');
```

### 1到N数字中由多少个1

```js
public static void main(String[] args) {

long n = 13;

int cnt = 0;

for (int i = 1; i <= n; i++) {

cnt += count(i);

}

System.out.println(cnt);

}

static int count(long number) {

int cnt = 0;

while (number > 0) {

cnt += number % 10 == 1 ? 1 : 0;

number /= 10;

}

return cnt;

}


function main(n) {
  var cnt = 0;
  for(var i = 0; i < n.length; i++) {
    cnt += count(i)
  }
}
function count(number) {
  var cnt = 0;
  while(number > 0) {
    cnt += number % 10 === 1 ? 1 : 0;
    number = number / 10;
  }
  return cnt;
}
```

### 实现bind

```js
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  return function F() {
    if (this instanceof F) {
      return new _this(...args, ..arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```

### 冒泡排序

```js
// 冒泡排序
function selection(arr) {
    var len = arr.length;
    var temp;
    for (var i = 0; i < len; i++ ) {
         for (var j = i + 1; j < len; j++ ) {
             if (arr[i] > arr[j]) {
                 temp = arr[i];
                 arr[i] = arr[j];
                 arr[j] = temp;
             }
         } 
    }
    return arr
}

var ret = selection([2,34,6,1])
console.info(ret)
```

### promise实现

```js
// 三种状态
const PENDING = 'pending';
const RESOLVED = 'resolved';
const REJECTED = 'rejected';

function MyPromise(fn) {
    let _this = this;
    _this.currentState = PENDING;
    _this.value =  undefined;
    _this.resolvedCallbacks = [];
    _this.rejectedCallbacks = [];


    _this.resolve = function (value) {
        if (value instanceof MyPromise) {
            return value.then(_this.resolve, _this.reject)
        }
        setTimeout(() => {
            if (_this.currentState === PENDING) {
                _this.currentState = RESOLVED;
                _this.value = value;
                _this.resolvedCallbacks.forEach(cb => cb());
            }
        })
    };


    _this.reject = function (reason) {
        setTimeout(() => {
            if (_this.currentState === PENDING) {
                _this.currentState = REJECTED;
                _this.value = reason;
                _this.rejectedCallbacks.forEach(cb => cb());
            }
        })
    }


    try {
        fn(_this.resolve, _this.reject);
    } catch(e) {
        _this.reject(e);
    }
}
```

### 最大公共子串

```js
function findSubStr(str1, str2){
    if (str1.length > str2.length) {
      var temp = str1;
      str1 = str2;
      str2 = temp;
    }
    var len1 = str1.length,
      len2 = str2.length;
    for (var j = len1; j > 0; j--) {
      for (var i = 0; i < len2 - j; i++) {
        var current = str1.substr(i, j);
        if (str2.indexOf(current) >= 0) {
          return current;
        }
      }
    }
    return "";
}
```

### 数组最大子段和

```js
function maxSum(arr) {
    var current = 0,
      sum = 0;
    for (var i = 0; i < arr.length; i++) {
      if (current > 0) {
        current += arr[i];
      } else {
        current = arr[i];
      }
      if (current > sum) {
        sum = current;
      }
    }
    return sum;
}
```

### 最长公共子序列的长度

```js
function lcs(str1, str2) {
    var len1 = str1.length;
    var len2 = str2.length;
    var dp = []; // 首先定义一个一维数组
    for (var i = 0; i <= len1; i++) {
      dp[i] = []; // 将一维数组升级为二维数组
      for (var j = 0; j <= len2; j++) {
        if (i == 0 || j == 0) {
          dp[i][j] = 0;
          continue;
        }
        if (str1[i - 1] == str2[j - 1]) { // dp 的维度为 (len1+1)*(len2+1),str 的维度为 (len1)*(len2)
          dp[i][j] = dp[i - 1][j - 1] + 1;
        } else {
          dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]); // 否则取当前位置上或左的最大数
        }
      }
    }
    return dp[len1][len2]; // 返回二维数组最后一个值
}
```

### 在一个数组中找出其中两项和相加后的和为num，存在时返回两个数的索引位置，否则false

```js
function fn(num = 0, arr = []) {
    for(let i = 0; i < arr.length; i++) {
        let diff = num - arr[1];
        let diffIndex = arr.indexOf(diff);
        if (diffIndex !== -1) {
            return [i, diffIndex];
        }
    }
    return false;
}
```