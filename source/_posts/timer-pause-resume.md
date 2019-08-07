---
title: timer-pause-resume
date: 2018-01-25 12:45:32
tags: javascript
categories:
    - cate
---

## setTimeout & setTimeout 暂停方案

管理setTimeout & setInterval 两个API时，会在顶级（全局）作用域创建一个timer的对象，他下面有两个数组成员—{sto, siv}, 用它们来分别存储需要管理的setTimeoutID/setIntervalID.如下：

```js
var timer = {
    sto: [],
    siv: []
}
```



使用setTimeout/setInterval时，这样调用：

```js
// 标记setTimeoutID
timer.sto.push(
    setTimeout(function() {console.log('3s')}, 3000);
);

// 标记 setIntervalID
timer.siv.push(
    setInterval(function() {console.log('1s')}, 1000)
)
```



在页面使用clearTimeout/clearInterval时，这样调用：

```js
// 批量清除 setTimeout
timer.sto.forEach(function() {console.log(sto)});
// 批量清除 setInterval
timer.siv.forEach(function() {console.log(siv)});
```



### 暂停 & 恢复，对timer进行扩展

```js
// 暂停所有的setTimeout & setInterval
timer.pause();
// 恢复所有的setTimeout & setInterval
timer.resume();
```

**扩展后的timer对象挂载6个基础的APIs**

- setTimeout
- setInterval
- clearTimeout
- clearInterval
- pause
- resume

### CreateJS 延伸

借用 tweenjs 创建 setTimeout 与 setInterval API。目的是 createjs.Ticker.paused 可以同步操作 setTimeout/setInterval


```js
// setTimeout
createjs.setTimeout = function(fn, delay) {
	createjs.Tween.get().wait(delay).call(fn);
}
//setInterval
createjs.setInterval = function(fn, delay) {
	createjs.Tween.get().wait(delay).call(fn).loop = 1; 
}
```



## 在createjs对象下挂载四个APIs

- setTimeout
- setInterval
- clearTimeout
- clearInterval

#### 用法：

```js
// 创建一个 setTimeout
let sto = createjs.setTimeout(function() {
  // to do
}, 3000); 

// 清除setTimeout
createjs.clearTimeout(sto); 


// 创建一个 setInterval
let siv = createjs.setInterval(function() {
  // to do
}, 1000); 

// 清除 setInterval
createjs.clearInterval(siv);
```

### demo

```js
var timer = (new function() {
    var stos = [], sivs = [], that = this; 
    this.setTimeout = function(fn, delay) { 
        var id = stos.length; 
        stos[id] = {
            fn: fn, 
            paused: false, 
            delay: delay, 
            start: new Date().getTime(),
            id: setTimeout(function() {
                fn && fn();
                timer.clearTimeout(id); 
            }, delay)
        }
        return id;
    }
    this.clearTimeout = function(id) {
        var sto = stos[id];
        if(sto === undefined) return false; 
        // 清空Timeout
        clearTimeout(sto.id);
        // 删除 id
        stos.splice(id, 1);
        return true;
    }
    this.pauseTimeout = function(id) { 
        var sto = stos[id]; 
        if(sto === undefined || sto.paused === true) return false; 
        // 标记暂停
        sto.paused = true; 
        // 清空Timeout
        clearTimeout(sto.id); 
        var elapse = new Date().getTime() - sto.start; 
        // 重置 delay
        sto.delay = sto.delay - elapse;  
        return true;  
    }
    this.resumeTimeout = function(id) {
        var sto = stos[id]; 
        if(sto === undefined || sto.paused === false) return false; 
        // 标记为恢复
        sto.paused  = false; 
        // 新建一个 timeout 表示继续 
        sto.id = stos[timer.setTimeout(sto.fn, sto.delay)].id; 
        return true;  
    }
    this.setInterval = function(fn, delay) {
        var id = sivs.length; 
        sivs[id] = {
            fn: fn, 
            paused: false, 
            delay: delay, 
            start: new Date().getTime(), 
            id: setInterval(fn, delay)
        }
        return id; 
    }
    this.clearInterval = function(id) {
        var siv = sivs[id]; 
        if(siv === undefined) return false; 
        // 清空Interval
        clearInterval(siv.id); 
        // 删除 id
        sivs.splice(id, 1); 
        return true; 
    }
    // 清空所有的 timeout
    this.cleanTimeout = function() {
        for(var i = 0; i < stos.length; ++i) {
            var sto = stos[id]; 
            // 清空Timeout
            sto === undefined || clearTimeout(sto.id); 
        }
        // 清空 stos 数组
        stos = []; 
    }
    // 清空所有的 interval
    this.cleanInterval = function() {
        for(var i = 0; i < sivs.length; ++i) {
            var siv = sivs[id]; 
            // 清空Timeout
            siv === undefined || clearInterval(siv.id); 
        }
        // 清空 stos 数组
        sivs = []; 
    }
    // 清空所有的 timeout 和 interval
    this.clean = function() {
        this.cleanTimeout(); 
        this.cleanInterval();
        return true; 
    }
    this.pauseInterval = function(id) {
        var siv = sivs[id]; 
        if(siv === undefined || siv.paused === true) return false; 
        // 标记暂停
        siv.paused = true; 
        // 清空 Interval
        clearInterval(siv.id); 
        var elapse = (new Date().getTime() - siv.start) % siv.delay; 
        // 添加一个 wait 属性
        siv.wait = siv.delay - elapse; 
        return true; 
    }
    this.resumeInterval = function(id) {
        var siv = sivs[id]; 
        if(siv === undefined || siv.paused === false) return false; 
        // 标记恢复
        siv.paused = false; 
        // 调一个 setTimeout 执行 wait 的时间
        this.setTimeout(function() {
        siv.fn(); 
        // 新建一个 interval 表示继续
        siv.id = sivs[timer.setInterval(siv.fn, siv.delay)].id; 
        }, siv.wait); 
    }

    // 综合 API
    this.pause = function(id) {
        if(id === undefined) {
            // 表示暂停全部 timeout & interval
            stos.forEach(function(item, id) {
                that.pauseTimeout(id); 
            }); 
            sivs.forEach(function(item, id) {
                that.pauseInterval(id); 
            }); 
            return true; 
        }
        else if(stos[id] !== undefined) {
            this.pauseTimeout(id); 
            return true; 
        }
        else if(sivs[id] !== undefined) {
            this.pauseInterval(id); 
            return true; 
        }
        return false; 
    }
    this.resume = function(id) {
        if(id === undefined) {
            // 表示暂停全部 timeout & interval
            stos.forEach(function(item, id) {
                that.resumeTimeout(id); 
            }); 
            sivs.forEach(function(item, id) {
                that.resumeInterval(id); 
            }); 
            return true; 
        }
        else if(stos[id] !== undefined) {
            this.resumeTimeout(id); 
            return true; 
        }
        else if(sivs[id] !== undefined) {
            this.resumeInterval(id); 
            return true; 
        }
        return false; 
    }
}());
```

#### 注:

保证 createjs 有 tweenjs 模块。setTimeout/cleatTimeout/setInterval/clearInterval 都挂载在 createjs 全局对象上。