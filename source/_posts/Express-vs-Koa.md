---
title: Express-vs-Koa
date: 2019-04-12  10:00:58
tags: Node
categories: 
    - node-framework
    - interview
---

## Node框架

- Express，是一个基于Node.js平台的极简、灵活的web应用开发框架，主要基于Connect中间件，并且自身封装了路由、视图处理等功能，使用人数众多。
- Koa，主要基于co中间件，框架自身不包含任何中间件，很多功能需要借助第三方中间件解决。Express的团队又基于ES6的新特性重新开发web框架koa
  - 和Express相比，koa 1.0使用generator实现异步，代码看起来像同步的，解决了 “callback hell” 和麻烦的错误处理问题。
  - generator的本意并不是异步，koa团队又非常超前地基于ES7开发了koa2，和koa 1相比，koa2完全使用Promise并配合async来实现异步

### 具体示例差别

1. 创建一个基础的Web服务（两者的区别是路由处理Express是自身集成的，而Koa需要引入中间件）

   ```js
   // 1. Express
   var express = require('express')
   var app = express()
   
   app.get('/', function (req, res) {
       res.send('express')
   })
   app.listen(3000)
   
   
   
   // 2. Koa
   var koa = require('koa')
   var route = require('koa-route')
   var app = koa()
   
   app.use(route.get('/', function *(){
       this.body = 'koa'
   }))
   app.listen(3000)
   ```

2. Views

   - Express自身集成了视图功能，提供了consolidate.js功能，支持几乎所有JS模板引擎，并提供了视图设置的便利方法

   - Koa需要引入co-views中间件

     ```js
     // 1. Express
     var express = require('express')
     var app = express()
     
     app.set('views', __dirname + '/views')
     app.set('view engine', 'jade')
     
     app.get('/', function (req, res) {
     res.render('index', {
         title: 'bilibili'
     })
     })
     
     
     
     // 2. Koa
     var koa = require('koa')
     var route = require('koa-route')
     var views = require('co-views')
     
     var render = views(__dirname + '/views', {
     default: "jade"
     })
     
     var app = koa()
     
     app.use(route.get('/', function *() {
     this.body = yield render('index', {
         title: 'bilibili'
     })
     }))
     ```

3. HTTP Request
   两个框架都封装了HTTP Request对象，区别是Koa v1使用this取代Express的req、res

```js
// 1. Express
var app = require('express')()

app.get('/room/:id', function (req, res) {
    console.log(req.params)
})

// 获取post数据需要body-parser中间件
var bodyParser = require('body-parser')
app.use(bodyParser.json())
app.post('/sendapi', function (req, res) {
    console.log(req.body)
})



// 2. Koa
var app = require('koa')()
var route = require('koa-route')

app.use(route.get('/room/:id', function *(){
    console.log(this.req.query)
}))

// 获取POST数据需要co-body中间件
var parse = require('co-body')
app.use(route.post('/api', function *() {
    var post = yeild parse(this.request)
    console.log(post)
}))
```

### Express 和 Koa的区别

1. 异步流程控制
   - Express采用callback来处理异步，Koa v1采用generator，Koa v2采用async/await.
   - generator和async/await使用同步的写法来处理异步，明显好于callback和promise；
   - async/await在语义化上又要比generator更强
2. 错误处理
   - Express使用callback捕获异常，对于深层次的异常捕获不了
   - Koa使用try catch，能更好地解决异常捕获
3. 总结
   - Express
     - 优点：线性逻辑，通过中间件形式把业务逻辑细分、简化，一个请求进来经过一系列中间件处理后再响应给用户，清晰明了。
     - 缺点：基于 callback 组合业务逻辑，业务逻辑复杂时嵌套过多，异常捕获困难。
   - Koa
     - 优点：首先，借助 co 和 generator，很好地解决了异步流程控制和异常捕获问题。其次，Koa 把 Express 中内置的 router、view 等功能都移除了，使得框架本身更轻量。
     - 缺点：社区相对较小
   - koa的中间件模式与express不一样，koa是洋葱型，express是直线型
     - 

## Koa框架

Koa 就是一种简单好用的 Web 框架。它的特点是优雅、简洁、表达力强、自由度高。所有功能都通过插件实现

检查Node版本，Koa必须使用7.6以上的版本。

### 用法：

- 架设HTTP服务： var app = new Koa(); app.listen(3000)
- Context对象：标识一次对话的上下文(包括HTTP请求和回复)。通过加工这个对象，可以控制返回给用户的内容
  - Context.response.body属性就是发送给用户的内容
  - ctx.request代表 HTTP Request；ctx.response代表 HTTP Response
- HTTP response的类型
  - Koa默认返回的类型是text/plain
  - 若想要返回其他类型的内容，可以先用ctx.request.accepts判断客户端希望接受的数据类型（根据HTTP Request的Accept字段），然后使用ctx.response.type指定返回类型
- 网页模板
  实际开发中，返回给用户的网页都写成模板文件。可以先让Koa限度无模板文件，然后将这个模板返回给用户。

```js
const fs = require('fs');

const main = ctx => {
    ctx.response.type = 'html';
    ctx.response.body = fs.createReadStream('./demos/template.html');
}
```

### 路由

- 原生路由

  网站一般都有多个页面。通过ctx.request.path可以获取用户请求的路径，由此实现简单的路由。

- koa-route模块

  使用封装好的koa-route模块

  ```js
  const route = require('koa-route');
  
  const main = ctx => {
      ctx.response.body = 'Hello World';
  }
  
  app.use(route.get('/', main));
  ```

- 静态资源

  koa-static模块封装静态资源的请求

  ```js
  const path = require('path');
  const serve = require('koa-static');
  
  const main = serve(path.join(__dirname));
  app.use(main);
  ```

- 重定向
  服务器需要重定向（redirect）访问请求。ctx.response.redirect()方法可以发出一个302跳转。

  ```js
  const redirect = ctx => {
      ctx.response.redirect('/');
      ctx.response.body = '<a href="/">Index Page</a>';
  };
  app.use(route.get('/redirect', redirect));
  ```

### 中间件

- Logger功能

  Logger功能可以拆分成一个独立函数。

  ```js
  const logger = (ctx, next) => {
      console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
      next();
  }
  
  app.use(logger);
  ```

- 中间件

  如上述的logger函数就叫‘中间件’，它处于HTTP Request和HTTP Response中间，用来实现某种中间功能。app.use()用来加载中间件。

  Koa 所有的功能都是通过中间件实现的。每个中间件默认接受两个参数，第一个参数是 Context 对象，第二个参数是next函数。只要调用next函数，就可以把执行权转交给下一个中间件。

- 中间件栈

  多个中间件栈会形成一个栈结构，以“先进先出”的顺序执行。

  如果中间件内部没有调用next函数，那么执行权就不会传递下去

  > 

  ```
  最外层的中间件首先执行。
  调用next函数，把执行权交给下一个中间件。
  ...
  最内层的中间件最后执行。
  执行结束后，把执行权交回上一层的中间件。
  ...
  最外层的中间件收回执行权之后，执行next函数后面的代码。
  ```

- 异步中间件

  有异步操作（比如读取数据库），中间件就必须写成 async 函数。

  ```js
  const fs = require('fs.promised');
  const Koa = require('koa');
  const app = new Koa();
  
  const main = async function (ctx, next) {
      ctx.response.type = 'html';
      ctx.response.body = await fs.readFile('./demos/template.html', 'utf8');
  };
  
  app.use(main);
  app.listen(3000);
  ```

- 中间件的合成

  koa-compose模块可以将多个中间件合成为一个。

  ```js
  const compose = require('koa-compose');
  const middlewares = compose([logger, main]);
  app.use(middlewares);
  ```

### 错误处理

- 500错误

  koa 提供了ctx.throw()方法，用来抛出错误，ctx.throw(500)就是抛出500错误

- 404错误

  如果将ctx.response.status设置成404，就相当于ctx.throw(404)

- 处理错误的中间件

  使用try…catch将其捕获

  让最外层的中间件，负责所有中间件的错误处理

  ```js
  const handler = async (ctx, next) => {
      try {
          await next();
      } catch (err) {
          ctx.response.status = err.statusCode || err.status || 500;
          ctx.response.body = {
              message: err.message
          };
      }
  };
  
  app.use(handler);
  ```

- error事件的监听

  运行过程中一旦出错，Koa 会触发一个error事件。监听这个事件，也可以处理错误。

- 释放 error 事件

  如果错误被try…catch捕获，就不会触发error事件。这时，必须调用ctx.app.emit()，手动释放error事件，才能让监听函数生效。

### Web App的功能

- Cookies

  ctx.cookie用来读写Cookie。ctx.cookies.set();

- 表单

  表单就是 POST 方法发送到服务器的键值对。koa-body模块可以用来从 POST 请求的数据体里面提取键值对。

- 文件上传

  koa-body模块还可以用来处理文件上传