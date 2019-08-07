---
title: express
date: 2019-04-12 10:10:30
tags: Node
categories: 
	- node-framework
---

## 一、内容

Express是一个node.js Web应用框架，提供了一系列特性帮助创建各种web应用，和丰富的HTTP工具。

Express 框架核心特性：

- 可以设置中间件来相应HTTP请求；
- 定义了路由表用于执行不同的HTTP请求动作；
- 可以通过向模块传递参数来动态渲染HTML页面

安装Express： npm install express –save

安装Express框架时，需与以下几个模块一起安装：

- body-parser：node.js 中间件，用于处理 JSON, Raw, Text 和 URL 编码的数据。
- cookie-parser： 是一个解析Cookie的工具。通过req.cookies可以取到传过来的cookie，并把它们转成对象。
- multer： node.js 中间件，用于处理 enctype=”multipart/form-data”（设置表单的MIME编码）的表单数据。

请求和响应：

Express 应用使用回调函数的参数： request 和 response 对象来处理请求和响应的数据。

- Request 对象 - request 对象表示 HTTP 请求，包含了请求查询字符串，参数，内容，HTTP 头部等属性。常见属性有：
  - req.app：当callback为外部文件时，用req.app访问express的实例
  - req.baseUrl：获取路由当前安装的URL路径
  - req.body / req.cookies：获得「请求主体」/ Cookies
  - req.fresh / req.stale：判断请求是否还「新鲜」
  - req.hostname / req.ip：获取主机名和IP地址
  - req.originalUrl：获取原始请求URL
  - req.params：获取路由的parameters
  - req.path：获取请求路径
  - req.protocol：获取协议类型
  - req.query：获取URL的查询参数串
  - req.route：获取当前匹配的路由
  - req.subdomains：获取子域名
  - req.accepts()：检查可接受的请求的文档类型
  - req.acceptsCharsets / req.acceptsEncodings / req.acceptsLanguages：返回指定字符集的第一个可接受字符编码
  - req.get()：获取指定的HTTP请求头
  - req.is()：判断请求头Content-Type的MIME类型
- Response 对象 - response 对象表示 HTTP 响应，即在接收到请求时向客户端发送的 HTTP 响应数据。常见属性有：
  - res.app：同req.app一样
  - res.append()：追加指定HTTP头
  - res.set()在res.append()后将重置之前设置的头
  - res.cookie(name，value [，option])：设置Cookie
    opition: domain / expires / httpOnly / maxAge / path / secure / signed
  - res.clearCookie()：清除Cookie
  - res.download()：传送指定路径的文件
  - res.get()：返回指定的HTTP头
  - res.json()：传送JSON响应
  - res.jsonp()：传送JSONP响应
  - res.location()：只设置响应的Location HTTP头，不设置状态码或者close response
  - res.redirect()：设置响应的Location HTTP头，并且设置状态码302
  - res.render(view,[locals],callback)：渲染一个view，同时向callback传递渲染后的字符串，如果在渲染过程中有错误发生next(err)将会被自动调用。callback将会被传入一个可能发生的错误以及渲染后的页面，这样就不会自动输出了。
  - res.send()：传送HTTP响应
  - res.sendFile(path [，options] [，fn])：传送指定路径的文件 -会自动根据文件extension设定Content-Type
  - res.set()：设置HTTP头，传入object可以一次设置多个头
  - res.status()：设置HTTP状态码
  - res.type()：设置Content-Type的MIME类型

## 二、guide

### 路由

1. 路由表示应用程序端点（URI）的定义以及端点响应客户机请求的方式。决定了由谁(指定脚本)去响应客户端请求

2. 路由方法

   路由方法派生自HTTP方法之一，附加到express类的实例。

   - Express 支持对应于HTTP方法的以下路由方法：get、post、put…

     app.get(‘/‘, function )

   - 特殊的路由方法：app.all()，并非派生自HTTP方法。该方法用于在所有请求方法的路径中装入中间件函数。

     app.all(‘/‘, function (req, res){

     ```
     next();
     ```

     })

3. 路由路径

   路由路径和请求方法相结合， 用于定义可以在其中提出请求的端点。路由路径可以是字符串、字符串模式或正则表达式。

   示例：

   ```
   app.get('/', function (req, res) { // demo01:（此路由路径将请求与跟路由/匹配）
       res.send('root');
   });
   app.get('/ab?cd', function(req, res) { // demo02: （路由路径将匹配acd和abcd）
       res.send('ab?cd');
   });
   ```

4. 路由处理程序（中间件系统）
   可以提供多个回调函数，类似于中间件的行为方式来处理请求。这些回调函数可能调用next(‘route’)来绕过剩余的路由回调。

   - 路由处理程序的形式可以是一个函数、一组函数或者两者的结合。

   - 多个回调函数可以处理一个路由（确保指定next对象）

     ```
     app.get('/example/b', function (req, res, next) {
         console.log('lala');
         next();
     }, function (req, res) {
         res.send('Hello');
     })
     ```

   - 一组回调函数可以处理一个路由

     ```
     var cb0 = function (req, res, next) {
         console.log('cb0');
         next();
     }
     var cb1 = function (req, res, next) {
         console.log('cb1');
         next();
     }
     var cb2 = function (req, res, next) {
         res.send('Hello from 2');
     }
     app.get('/example/c', [cb0, cb1, cb2])
     ```

5. 响应方法

   响应对象(res)的方法可以向客户机发送响应，并终止请求/响应循环。如没有从路由处理程序调起其中任何方法，客户机请求将保持挂起状态。

   - res.download() // 提示将要下载文件
   - res.end() // 结束响应
   - res.json() // 发送JSON响应
   - res.jsonp() // 在JSONP的支持下发送JSON响应
   - res.redirect() // 重定向请求
   - res.render() // 呈现视图模板
   - res.send() // 发送各种类型的响应
   - res.sendFile() // 以八位元流形式发送文件。
   - res.sendStatus() // 设置响应状态码并以响应主体形式发送其字符串表示

6. app.route()

   使用app.route()为路由路径创建可链接的路由处理程序。在单一位置指定路径，可减少冗余和输入错误。

   使用app.route()定义的链式路由处理程序的示例

   ```
   app.route('/book')
       .get(function(req, res) {
           res.send('Get a data');
       })
       .post(function(req, res) {
           res.send('Add a data');
       })
       .put(function(req, res) {
           res.send('update a data');
       })
   ```

7. express.Router

   使用express.Router类来创建可安装的模块化路由程序。Router实例是完整的中间件和路由系统；

8. 编写中间件用于Express 应用程序

   中间件函数能够访问请求对象（req），响应对象（res）以及应用程序的请求/响应循环中的下一个中间函数。下一个中间件函数通常由名为 next 的变量来表示。

- 中间件函数可以执行任务：
  - 执行任何代码
  - 对请求和响应对象进行更改。
  - 结束请求/响应循环。
  - 调用堆栈中的下一个中间件函数
- 中间件类型：
  - 应用层中间件
  - 路由层中间件
  - 错误处理中间件
  - 内置中间件
  - 第三方中间件
- 应用层中间件
  1. 使用app.use()和app.METHOD()函数将应用层中间件绑定到应用程序对象的实例（METHOD是中间件函数处理的请求的小写HTTP方法：GET、PUT 或 POST）
  2. 一般有两种参数：路由路径及其处理程序函数(中间件系统)
  3. 跳过路由器中间件堆栈中剩余的中间件函数，调用 next(‘route’) 将控制权传递给下一个路由。仅在使用 app.METHOD() 或 router.METHOD() 函数装入的中间件函数中有效。
- 路由层中间件： 使用 router.use() 和 router.METHOD() 函数装入路由器层中间件
- 错误处理中间件：错误处理函数有四个自变量而不是三个，专门具有特征符 (err, req, res, next)
- 内置中间件
- 第三方中间件：使用第三方中间件向 Express 应用程序添加功能。
  - 安装具有所需功能的 Node.js 模块，然后在应用层或路由器层的应用程序中将其加装入。
  - 第三方中间件列表：
    - body-parser
    - cookie-parser
    - cookie-session
    - passport：用于认证的 Express 中间件模块
    - serve-static：用于提供静态内容的模块
    - express-session