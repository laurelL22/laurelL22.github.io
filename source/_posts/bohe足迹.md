---
title: bohe足迹
date: 2018-04-19 14:32:46
tags: doc
categories:
    - cate-summary
---


## 一、聊天消息由下向上冒效果

### scrollIntoView & scrollIntoViewIfNeeded

- Element.scrollIntoView(): 让当前的元素滚动到浏览器窗口的可视区域内。
- Element.scrollIntoViewIfNeeded(): 将不在浏览器窗口的可见区域内的元素滚动到浏览器窗口的可见区域。但如果该元素已经在浏览器窗口的可见区域内，则不会发生滚动。

### scrollIntoView

两种参数：

- Boolean
  - 为true，元素的顶端将和其所在滚动区的可视区域的顶端对齐。
  - 为false，元素的底端将和可视区域的底端对齐
- Object
  - block： start | end （同上面的布尔值一样）
  - behavior： auto | instant | smooth （决定页面滚动行为）
  - **有些浏览器不支持behavior取值为smooth**

[scrollIntoView](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollIntoView)

### scrollIntoViewIfNeeded

区别：

- scrollIntoViewIfNeeded比较随机，如果元素在可视区域，调用时，页面不会发生滚动。
- scrollIntoViewIfNeeded只有Boolean型参数，没有动画效果。
  - true为默认值，不是滚动到顶部，而是让元素在可视区域中**居中对齐**；
  - false时元素可能**顶部或底部**对齐
  - 元素处于部分可见，无论参数值是什么，都会滚动到元素与可视区域顶部或底部对齐，取决于元素离哪端更近

### scroll-behavior

https://css-tricks.com/almanac/properties/s/scroll-behavior/

[demo](https://jsfiddle.net/laurel/3s8kekqs/)

## 二、CSS3 animation — 播放、暂停动画

animation-play-state: paused | running

可以通查询它来确定动画是否正在运行。值可以被设置为暂停和恢复的动画的重放。

```css
.animation {
    animation: move 2s linear infinite alternate;
}

#stop:checked ~ .animation {
    animation-play-state: paused;
    // -webkit-animation: none !important; // ios 替代方案
}
#play:checked ~ .animation {
    animation-play-state: running;
}

@keyframes move {
    0% {
        transform: translate(-100px, 0);
    }
    100% {
        transform: translate(100px, 0);
    }
}
```



[demo](https://jsfiddle.net/laurel/rhc3e6nw/)

### animation-play-state 在ios上不生效，在安卓上ok

- -webkit-animation-play-state: paused 在ios 替代方案：
  - -webkit-animation: none !important;
  - but 会还原动画效果，不能记录当前动画状态
- 添加父元素记录状态，彻底解决：
  https://www.jianshu.com/p/b6f71cdbe063

## 三、IOS移动端隐藏滚动条

[问题](https://github.com/o2team/H5Skills/issues/72)

app是IOS 内核，但是使用display: none;无效

```css
&::-webkit-scrollbar
    display: none
    visibility: hidden
```



解决方案：
方案1：（包一层container，高度拉高，再用上面的盖掉）

```css 
scroll-list
    overflow: hidden
    @include px2rem(height, 440)
    &-container
        @include px2rem(height, 455)
        white-space: nowrap
        overflow-x: scroll
```



方案2：把滚动条撑开，然后通过负外边距抵消撑开的部分，使得外容器高度不受影响

```html
<div class="slider">
    <ul></ul>
</div>

<style>
.slider {
    overflow: hidden;
}
.slider ul {
    padding-bottom: 10px;
    margin-bottom: -10px;
}
</style>
```

## 四、footer固定在页面底部

只要四行就解决

```css
body {
    display: flex;
    flex-flow: column;
    min-height: 100vh;
}
main {
    flex: 1;
}
<header>
    header
</header>
<main>
    main
</main>
<footer>
    footer
</footer>


// 法三: footer高度固定 + 绝对定位
body {
    min-height: 100%;
    position: relative;
    
}
main {
    padding-bottom: 100px;
}
footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 100px
}


// 法二: footer高度固定 + margin负值
body {
    min-height: 100%;
    position: relative;
    
}
main {
    padding-bottom: 100px;
    
}
footer {
    margin-top: -100px;
    width: 100%;
    height: 100px
}
```

https://segmentfault.com/a/1190000004453249

## 五、判断是否为iphonex

```js
function isIPhoneX () {
    return /iphone/gi.test(window.navigator.userAgent) && (screen.height === 812 && screen.width === 375);
};
```

## 六、取消浏览器就滚动条位置

history.scrollRestoration是新增的一个属性，值为auto|manual,默认为auto记录滚动条位置, 设为manual时就禁用记录位置的功能。
兼容性不乐观（至少iOS 10.3.2版本的浏览器不行）

```js
if ('scrollRestoration' in history) {
    history.scrollRestoration = 'manual';
}else{
    window.onunload= () => window.scrollTo(0,0);
}
```

## 七、axios 请求携带cookie信息

- 设置axios.defaults.withCredentials为true
- 服务器端需要配置header(“Access-Control-Allow-Credentials: true”)
- !!! 当Access-Control-Allow-Credentials设为true的时候，Access-Control-Allow-Origin不能设为星号*

## 八、移动端底部固定定位fixed & IOS上autofoucs失效，需要手动触发 && 移动端吸底/吸顶

ios下fixed元素容易定位出错,软键盘弹出时影响fixed元素定位,而android下不会。

ios下由于软键盘唤出后，页面fixed元素会失效，导致跟随页面一起滚动。假如-页面不会过长出现滚动，即便fixed元素失效，也无法跟随页面滚动，也就不会出现上面的问题。

- 通常将底部设置为fixed，当激活输入框的时候，将底部定位改为absolute，即可兼容ios和Android。

- ios：在激活输入框的时候，保证页面滚动至底部，执行：

  ```js
  setTimeout(() => {
      document.body.scrollTop = document.body.scrollHeight;
  }, 500);
  ```

- 点击按钮，触发输入框的focus事件

  ```js
  if (/(iphone|ipod|ipad)/gi.test(window.navigator.userAgent)) {
      this.$refs.inputbar.focus();
  }
  
  setTimeout(_ => { // fixed 安卓点击穿透
      if (/android/gi.test(window.navigator.userAgent)) {
          this.$refs.inputbar.focus();
      }
  }, 200);
  ```

- 但仍有体验问题

### 移动端吸底/吸顶

DOM 结构：

- container(外层)
  - wrapper
  - footer

sass:

```css
.container
    height: 100vh
    overflow: hidden
    display: flex
    flex-direction: column  

    .wrapper
        flex: 1
        overflow: auto

    .footer
        height: 50px
        width: 100vw
```

## 九、ios系统手机无法自动播放音频/视频， HTML5 audio autoplay无效

这个是苹果系统限制默认不允许自动播放音频/视频,需要点一下触发play()事件才能播放;

```js
window.onload = function() {
    document.getElementById('music').play();
}
```

------

android 4.2添加了允许用户手势触发音视频播放接口，该接口默认为 true ，即默认不允许自动播放音视频，只能是用户交互的方式由用户自己促发播放。

> mWebview.getSettings().setMediaPlaybackRequiresUserGesture(false);

## 十、浏览器后退不刷新

当点击后退时页面以缓存形式出现,而不是刷新后的,解决方法是用js:

```js
window.onpageshow = function(evt){
  if(evt.persisted){ 
     document.body.style.display ="none";
     location.reload();
  }
};
```

- onpageshow每次页面加载都会触发,无论是从缓存中加载还是正常加载,是和onload的区别;
- persisted判断页面是否从缓存中读出

## 十一、个别设备样式问题

- input的placeholder文本位置偏上： line-height: normal;

- 部分安卓机box-sizing 有上边框：删除box-sizing: border-box;

- 安卓H5的border-radius,圆角不生效 => border-raius: 10px; // 单位必须是px,不能为其他的rem单位

- CSS3 动画在安卓上卡顿：

  开启硬件加速：

  - opacity: 1
  - -webkit-backface-visibility: hidden

## 十二、屏幕像素比/屏幕缩放比

屏幕像素比 = window.devicePixelRatio

缩放比例 = 设备像素 / CSS像素 ===> screen.availWidth/window.innerWidth

## 十三、input的compositionstart 和 compositionend

在IOS中，input事件会截断非直接输入
（非直接输入：在输入汉字时，中间过程会输入拼音，每次输入一个字母会触发input事件，然而在没有点选择候选字或者点击【选定】按钮前，都属于非直接输入）

**需求：** 直接输入之后才触发input事件，需引出：compositionstart 和 compositionend
compositionstart 事件在用户开始进行非直接输入时触发，在非直接输入结束，也即用户点选候选词 或者 点击【选定】按钮之后，会触发compositionend事件

添加一个inputLock变量，当用户未完成直接输入前，inputLock 为true，不触发input事件中的逻辑，当用户完成有效输入之后，inputLock设置为false，触发input事件的逻辑。
compositionend 事件在input事件后触发的，在compositionend事件出发时，也要调用input事件处理逻辑

```js
inputHandle(e) {
    if (this.inputLock) {
        return;
    }

    let value = e.target.value;
    value && this.searchHandler(value);
},
onCompositionstart(e) {
    // 未完成直接输入前, inputLock 为true
    throttle(_ => {
        this.inputLock = true;
    }, 200);
},
onCompositionend(e) {
    // 完成有效输入后, inputLock 为false
    throttle(_ => {
        this.inputLock = false;
    }, 200);

    // compositionend 事件是在 input 事件后触发，在 compositionend事件触发时，也要调用 input处理逻辑
    let value = e.target.value;
    value && this.searchHandler(value);
},
```

## 十四、css文字和图标垂直居中

```html
<li>
    <span class="aligned">文字</span>
    <span class="icon aligned"></span>
</li>
```
```css
.icon {
    display: inline-block;
    width: 20px;
    height: 20px;
    background-color: #aaa;
    border: solid 1px #000;
}
.aligned {
    vertical-align: middle;
}
```

## 十五、createNamespacedHelpers 建基于某个命名空间的辅助函数

```
取消 api/action 或 api/getter 的写法
```
```js
import {createNamespacedHelpers} from 'vuex';

const {
    mapState,
    mapGetters,
    mapActions,
    mapMutations
} = createNamespacedHelpers('app');
```

## 十六、vue内静态资源引用路径
vue-html-loader和css-loader将非根URL转换为相对路径。为了将其视为模块路径，在前面加上~

```js
alias: {
    'src': resolve('src'),
    'assets': resolve('src/assets')
}

// vue文件：
<img class="logo" src="~assets/logo.png">
```

## 十七、axios 超时处理

```js
axios.interceptors.response.use(undefined, error => {
    if (error.code === 'ECONNABORTED' && error.message.indexOf('timeout') !== -1) {
        console.log('超时')
    }
})
```

## 十八、vue-router 路由切换，组件重用的坑

vue-router导航切换时，如果两个路由都渲染同个组件，组件会重用，组件的生命周期钩子不会再被调用，使得组件的一些数据无法根据path的改变得到更新。

解决：

- beforeRouteUpdate：对于/foo/:id,在/foo/1和foo/2之间跳转。当前路由改变，但组件被复用时调用（带有动态参数）
- watch ‘$route’：对于两路由共用同一个组件，如从/main跳到/get

```js
// todo
```
