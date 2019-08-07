---
title: Code-Snippet
date: 2019-04-22 10:47:32
tags:
categories: 
	- interview
---

### Agenda

- 实现Promise
- 实现promiseAll
- 实现bind
- 实现数据劫持
- 最大公共子串

### 实现Promise

```javascript
// 三种状态
const PENDING = 'pending';
const RESOLVED = 'resolved';
const REJECTED = 'rejected';

function MyPromise(fn) {
    let _this = this;
    _this.currentState = PENDING;
    _this.value = undefined;
    _this.resolvedCallbacks = [];
    _this.rejectedCallbacks = [];

    _this.resolve = function (value) {
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
MyPromise.prototype.then = function (onFullfilled,onRejected) {
    let _this = this;
    switch(_this.status) {
        case "pending":
            _this.onFullfilledArray.push(function(){
                onFullfilled(_this.value)
            });
            _this.onRejectedArray.push(function(){
                onRejected(_this.reason)
            });
        case 'resolved':
            onFullfilled(_this.value);
            break;
        case 'rejected':
            onRejected(_this.reason);
            break;
    }
}
```

### 实现promiseAll

```javascript
function promiseAll(promises) {
    return new Promise(function(resolve, reject){
        if (Array.isArray(promises)) {
            return reject(new TypeError('argument must be array'))
        }

        var countNum = 0;
        var promiseLength = promises.length;
        var resolvedvalue = new Array(promiseNum);

        for(let i = 0; i < promises; i++) {
            Promise.resolve(promise[i]).then(function(value){
                countNum++;
                resolvedvalue[i] = value;
                if (countNum === promiseNum) {
                    return resolve(resolvedvalue)
                }
            }, function(reason){
                return reject(reason);
            })
        }
    })
}
```

### 实现bind

```javascript
Function.prototype.myBind = function (context) {
  var _this = this;
  var args = [...arguments].slice(1);
  var bound = function () {
    arg = args.concat(...arguments);
    return _this.apply(context, arg);
  }
  var F = function() {}
  // 寄生式继承
  F.prototype = _this.prototype;
  bound.prototype = new F();
  return bound;
}
```

### 数据劫持

```javascript
// 通过Object.defineProperty对具体实例中的data实现代理，在get里面实现依赖收集，(每个数据都被生成一个Watcher实例，推进到Dep实例中)都推到一个Dep实例的subs数组里面，并将具体data里面的数据打上标记，表示是要实现响应式的数据;在set里面实现回调，当数据值发生变化的时候，Dep的notify方法会被调用，notice方法中会调用Watcher类的update方法，对具体数据进行更新操作，并调用render方法，将dom局部进行重新渲染更新

// Vue内部使用Object.defineProperty()来实现双向绑定，通过这个函数可以监听到set和 get的事件
// 实现一个简易的双向绑定，核心思路就是手动触发一次属性的 getter 来实现发布订阅的添加。
function observe(obj) {
    if(!obj || typeof obj !== 'object') {
        return
    }
    Object.keys(obj).forEach(key => {
        defineReactive(obj, key, obj[key]);
    })
}
function defineReactive(obj, key, val) {
    // 递归子属性
    observe(val)
    let dp = new Dep()
    Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: function reactiveGetter() {
            console.log('get value');
            // 将Watcher添加到订阅
            if (Dep.target) {
                dp.addSub(Dep.target)
            }
            return val
        },
        set: function reactiveSetter(newValue) {
            console.log('set value');
            val = newValue
            // 执行 watcher 的 update 方法
            dp.notify()
        }
    })
}


// 通过Dep解耦
class Dep {
    constructor() {
        this.subs = []
    }
    addSub(sub) { // 依赖收集,推到Dep实例的subs数组里面
        // sub是Watcher实例
        this.subs.push(sub)
    }
    notify() {
        this.subs.forEach(sub => sub.update())
    }
}
// 全局属性，通过该属性配置Watcher
Dep.target = null

class Watcher {
    constructor(obj, key, cb) {
        Dep.target = this // 将Dep.target指向自己
        this.cb = cb
        this.obj = obj
        this.key = key
        this.value = obj[key] // 然后触发属性的getter添加监听
        Dep.target = null // 最后将Dep.target置空
    }
    update() {
        // 获得新值
        this.value = this.obj[this.key]
        // 调用update方法更新Dom
        this.cb(this.value)
    }
}



// call
var data = { name: 'yck' }
observe(data)
new Watcher(data, 'name', update) // data数据生成一个Watcher实例
data.name = 'yyy'
```

### JS 实现DOM对象

```javascript
export default class Element {
    constructor(tag, props, children, key) {
        this.tag = tag
        this.props = props
        if (Array.isArray(children)) {
            this.children = children // 给每个节点一个标识，作为判断是同一个节点的依据
        } else if (isString(children)) {
            this.key = chidlren
            this.chidlren = null
        }
        if (key) this.key = key
    }
    // 渲染
    render() {
        let root = this._createElement(
            this.tag,
            this.props,
            this.children,
            this.key
        )
        document.body.appendChild(root)
        return root
    }
    create() {
        return this._createElement(this.tag, this.props, this.children, this.key)
    }
    // 创建节点
    _createElement(tag, props, child, key) {
        // 通过tag创建节点
        let el = document.createElement(tag)
        // 设置节点属性
        for (const key in props) {
            if (props.hasOwnProperty(key)) {
                const value = props[key]
                el.setAttribute(key, value)
            }
        }
        if (key) {
            el.setAttribute('key', key)
        }
        // 递归添加子节点
        if (child) {
            child.forEach(ele => {
                let child
                if (ele instanceof Element) {
                    child = this._createElement(
                        ele.tag,
                        ele.props,
                        ele.children,
                        ele.key
                    )
                } else { // 文本节点
                    child = document.createTextNode(ele)
                }
                el.appendChild(child)
            })
        }
        return el
    }
}
```

### 最大公共子串

```javascript
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