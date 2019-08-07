---
title: webpack-path-config
date: 2018-04-27 14:30:09
tags: webpack
---

[link](http://www.qinshenxue.com/article/20170315092242.html)

## 关于webpack 路径参数的配置

### convention

[path](http://javascript.ruanyifeng.com/nodejs/path.html)

- __dirname: 当前文件所在目录

- process.cwd(): node当前运行目录，webpack运行所在目录

- path.join(): 用于连接路径（依据当前系统的路径分割符）

- path.resolve(): 将相对路径转为绝对路径

- accessSync(): 用于同步读取一个路径

- path.relative(): 返回第二个参数相对于第一个路径的相对路径。接收两个参数，两个参数都是绝对路径。

- path.parse(): 可以返回路径各部分的信息

  ```js
  var filePath = '/dir/file.json'
  path.parse(filePath).base; ()
  path.parse(filePath).name
  ```

> **dirname, process.cwd(), path.resolve(dirname, ‘../‘)**
> /Users/baidu/ai-voice/build /Users/baidu/ai-voice /Users/baidu/ai-voice

### context

webpack编译时的基础目录，入口起点（entry）会相对于此目录查找。

默认为当前目录：

> this.set(‘context’, process.cwd) // process.cwd() 即为webpack运行所在的目录