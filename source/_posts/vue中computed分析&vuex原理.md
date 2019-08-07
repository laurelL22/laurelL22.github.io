---
title: vue中computed分析&vue原理
date: 2019-04-12 12:20:36
tags: framework
---


### vuex原理

vuex依赖于vue的computed依赖检测系统以及插件系统

Vuex.store除了初始化以外还有重置VM方法，即resetStoreVM(this, state).
本质是将传入的state作为一个隐藏的vue组件的data，commit操作本质是修改这个组件的data值。结合computed，修改被defineReactive代理的对象值后，会将其收集到的依赖的watcher中的dirty设置为true，等下一次访问该watcher中的值后重新获取最新值。可推出：store._vm.$data.$$state === store.state

vuex的实现方式使用了vue自身的响应式设计，依赖监听、依赖收集都属于vue对对象Property set get方式的代理劫持。即vuex工作原理：vuex中的store本质就是没有template的隐藏着的vue组件

### computed

- 计算属性如何与属性建立依赖关系
- 属性发生变化如何通知到计算属性重新计算

依赖收集、动态计算的流程：

1. data属性初始化getter setter
2. computed 计算属性初始化，提供的函数用作属性(vm.reverseMsg)的getter
3. 当首次获取计算属性(vm.reverseMsg)的值时，Dep开始依赖收集
4. 在执行msg getter方法时，如果Dep处于依赖收集状态，则判定 msg为reverseMsg的依赖，并建立依赖关系
5. 当 msg 发生变化时，根据依赖关系，触发 reverseMsg 的重新计算。

### 源码分析

- 初始化组件的state方法：initState
  - 当组件实例化时会自动触发，主要完成了初始化data、props、computed、watch等属性，以及initData和initComputed
  - initData
    - 将vue的data传入observer方法（该方法本质是实例化了一个Observer对象）
    - Observer里的重点：new Dep对象；给data的所有属性调用defineReactive
    - defineReactive是vue实现 MDV(Model-Driven-View)的基础，就是代理数据的get，set方法。当数据修改或获取的时候，能够感知
      - 在给具体属性调用defineReactive方法时，都会为该属性生成唯一的dep对象。
      - 重新定义data当中的属性，对get和set进行代理
      - get里收集依赖，dep.depend() // reversedMessage为什么会跟着message变化的原因
        - dep.depend()是将该dep加入watcher的newDeps中,同时将所访问当前属性的dep对象中的subs插入当前Dep.target的watcher
      - set里通知依赖进行更新，dep.notify()
        - notify方法即将加入该dep的watcher全部更新，即当修改data中某个属性值时，会同时调用dep.notify()来更新依赖该值的所有watcher
    - Dep 是 vue 实现的一个处理依赖关系的对象，主要起到一个纽带的作用，就是连接 reactive data 与 watcher
      - 重点方法：addSub()；depend()；notify() // 更新watcher 的值
      - 当首次计算 computed 属性的值时，Dep 将会在计算期间对依赖进行收集
      - pushTarget：在一次依赖收集期间，如果有其他依赖收集任务开始，将会把当前 target 暂存到 targetStack，先进行其他 target 的依赖收集，
      - popTarget: 当嵌套的依赖收集任务完成后，将 target 恢复为上一层的 Watcher，并继续做依赖收集
  - initComputed
    - 对每一个属性都生成了一个属于自己的Watcher实例，并将 { lazy: true }作为options传入
      - 在computed生成的watcher，会将watcher的lazy设置为true,以减少计算量。因此，实例化时，this.dirty也是true,标明数据需要更新操作
      - 先记住现在computed中初始化对各个属性生成的watcher的dirty和lazy都设置为了true。同时，将computed传入的属性值（一般为funtion）,放入watcher的getter中保存起来。
    - 对每一个属性调用了defineComputed方法(本质和data一样，代理了自己的set和get方法)
      - 当第一次访问computed中的值时，会因为初始化watcher.dirty = watcher.lazy的原因，从而调用watcher.evalute()方法，evalute()方法就是调用了watcher实例中的get方法以及设置dirty = false,我们将这两个方法放在一起
        - get方法： 调用了pushTarget方法，其作用就是将Dep.target设置为所传入的watcher,即所访问的computed中属性的watcher
        - this.getter 在Watcher构建函数中提到，本质就是用户传入的方法，也就是说，this.getter.call(vm, vm)就会调用用户自己声明的方法，那么如果方法里面用到了 this.data中的值或者其他被用defineReactive包装过的对象，那么，访问this.data.或者其他被defineReactive包装过的属性，是不是就会访问被代理的该属性的get方法（computed的watcher依赖了this.data的dep）
- 拆解获取依赖并更新的过程：
  - 初始化 data和computed，分别代理其get和set方法，对data中的所有属性生成唯一的dep实例
  - 对computed中的属性A生成唯一的watcher，并保存到vm._computedWatchers中
  - 访问计算属性A，设置Dep.target指向计算属性A的watcher，调用该属性具体的方法A()
  - 方法中访问属性this.a，即会调用this.a代理的get方法，将this.a的dep加入计算属性A的watcher，同时该dep中的subs添加这个watcher
  - 设置vm.a = ‘new a’，调用a代理的set方法触发dep的notify方法
  - 因为是computed属性，只是将watcher中的dirty设置为true
  - 查询计算属性vm.A，访问其get方法时，得知计算属性A的watcher.dirty为true,调用watcher.evaluate()方法获取新的值。