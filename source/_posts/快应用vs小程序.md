---
title: 快应用vs小程序
date: 2020-07-10 11:58:19
tags:
---

## 快应用 与 微信小程序的差异

### 组件差异

- 组件缺失

    小程序支持的form组件，但快应用规范目前没有该组件

- 属性缺失

    小程序的一些属性，在快应用上并不支持。如小程序的 textarea 组件支持auto-height 属性，但快应用的 textarea 组件却不支持auto-height

#### 组件转换

- 组件属性转换

    - 组件事件属性转换
    - 组件特征属性转换
    - 组件公共属性转换，如hidden

- 组件tag转换

- 组件语法支持

    - 子组件内容兼容
    - 文本组件插入文本内容

- 不支持的组件注释说明

### 样式差异

- 样式布局，只支持flex；
- 伪类选择器，只支持的选择器有':disabled', ':checked', ':focus', ':active', ':visited', ':autoplay'；
- 其他不支持样式等

#### 样式转换

- 转换 css 选择器 selector
- 转换 css 规则 declaration
- 转换 css 样式值
- 不支持的选择器、样式规则添加注释


### js脚本差异

- vm数据更新： this.setData()、this.data.xxx = xxx
- getApp() 小程序APP实例
- 生命周期函数
- Page/Component 构造器
- 组件的事件参数统一 evt.detail
- wx apis vs. 快应用api


#### apis 转换

所有的api 做polyfill `src/polyfill/script`，并将前缀改为qa.


### 配置文件差异

- app.json 文件 -> manifest.json
- app.js 文件 -> app.ux
- app.wxss 文件 -> app.css


### 最后

[小程序转快应用工具](https://www.npmjs.com/package/@quickapp-eco/qa-transformer)
