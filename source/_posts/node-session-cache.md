---
title: node-session-cache
date: 2018-05-24 14:16:24

---

[link](https://techblog.toutiao.com/2017/11/29/nodezhong-sessionchi-jiu-hua-yu-redishuan-cun/)

## node中Session持久化与Redis缓存

> HTTP协议和TCP/IP协议组中其他协议相同，用于客户端和服务端之间的通信，HTTP是一种无状态协议，及协议本身不保存客户端和服务端的通信状态，即在HTTP这个级别，协议不会对请求或响应做持久化处理，也是为了更快的处理大量事务，确保协议的可伸缩性。

**cookie：** 为了解决HTTP的无状态，引入了Cookie技术，Cookie技术通过在请求和响应报文中写入cookie信息来控制客户端的状态。

**session：** 为了跟踪客户端的状态，服务端 借助于客户端的cookie和后端存储实现的会话状态。Session机制决定了当前客户只会获取到自己的Session，而不会获取别人的Seesion

## cookie

1. 制定cookie机制，用于实现客户端和服务器之间的状态共享。

- Cookie 是解决HTTP无状态性的有效手段，服务器可以设置（set-cookie）或读取cookie中所包含的信息
- 当用户登录后，服务器会发送包含登录凭据的cookie到用户浏览器客户端，而浏览器对该Cookie进行某种形式的存储（内存或硬盘）
- 用户再次访问该网站时，浏览器会发送该Cookie（Cookie未到期时）到服务器，服务器对该凭据进行验证，合法时使用户不必输入用户名和密码就可以直接登录

1. 实现原理

Cookie定义了HTTP请求头和HTTP响应头，客户端和服务端通过这些头信息进行状态交互

- 客户端第一次请求：服务器端如果需要记录用户信息，才会在响应信息中返回Set-cookie响应头
- 客户端会根据响应头存储Cookie信息
- 客户端再次请求：会在请求头上带上存储的cookie信息，服务端通过cookie信息识别用户

1. cookie类别

**cookie总是存储在客户端（浏览器），根据其存储位置可以分切：内存式cookie、硬盘式cookie**

内存式：存储在内存中，浏览器关闭后清除，也非持久化存储（会话cookie）

```
- cookie不包含到期日期，则可视为会话cookie。会话cookie存储在内存中，绝不会写入磁盘。当浏览器关闭时，cookie将从此永久丢失
```

硬盘式：保存在硬盘中，浏览器关闭后不会清除，除非手动清除或到了过期时间，也将持久存储（持久cookie）

```
- cookie包含到期日期，则可视为持久性cookie。在指定的到期日期，cookie将从磁盘中删除
```

> 通常可以通过expires到期时间来区分

1. HTTP协议中为cookie服务的首部字段

Set-cookie: 响应首部字段，开始状态管理所使用的Cookie信息

| 属性         | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| NAME-VALUE   | 赋予Cookie的key / value (必需)                               |
| expires=DATE | 指定浏览器可以发送Cookie的有效期（默认为浏览器关闭前为止）   |
| path=PATH    | 将服务器上的文件目录作为Cookie的使用对象（默认为文档所在的文件目录） |
| domain=域名  | 作为Cookie使用对象的域名（默认为Cookie的服务器域名）         |
| Secure       | 仅在HTTPS安全通信时才会发送Cookie                            |
| HttpOnly     | 加以限制，使Cookie不能被javascript脚本访问。可以放置跨站脚本攻击对cookie信息窃取 |

Cookie：请求首部字段，服务端接受到的cookie信息

1. cookie-parser

cookie-parser 是node中用于操作cookie的中间件

```js
function(request, response, next) {
    res.cookie(name, value [,options])
}
```
```
name: cookie名
value: cookie值
options: set-cookie 选项
    domain：cookie在什么域名下有效，类型为String。默认为网站域名

    expires：cookie过期时间，类型为Date。如果没有设置或者设置为0，那么该cookie只在这个这个session有效，即关闭浏览器后，这个cookie会被浏览器删除

    httpOnly：只能被web server访问，类型Boolean

    maxAge：实现expires的功能，设置cookie过期的时间，类型为String，指明从现在开始，多少毫秒以后，cookie到期

    path：cookie在什么路径下有效，默认为'/'，类型为String

    secure：只能被HTTPS使用，类型Boolean，默认为false

    signed：使用签名，类型Boolean，默认为false。[express会使用req.secret来完成签名，需要cookie-parser配合使用]
```
```js
// 1.引入
var cookieParser = require('cookie-parser'); // 引入模块
app.use(cookieParser()); // 挂载中间件（实例化）

// 2.创建
res.cookie(name, value [,options]);
{
    'maxAge': 9000, // 有效时长（毫秒）
    'signed': false // 默认为false，表示是否签名（Boolean）
}

// 3. 获取cookie
var cookies = req.cookies // 获取cookies集合
var value = req.cookies.key // 获取名称未key的cookie的值

// 4. 删除cookie
res.clearCookie(name [,options]) // name 是cookie名，options与创建cookie时所传一致

// 5. 签名
// 签名可以提高安全性
// 使用签名生成cookie的方法，大同小异，修改上文

app.use(cookieParser('yog')); // 需要穿一个自定义字符串作为secret

// 创建cookie的options中，必填signed: true
res.cookie(name, value, {'signed': true});
var cookies = req.sifnedCookies // 获取cookie集合
var value = req.signedCookies.key // 获取名称为key的cookie值
```

## session

1. Session 需要借助Cookie实现，Session数据存储在服务端，而只在Cookie中存储一个SessionId，乐意保证安全性和降低服务器负载

2. express-session
   express-session 真正在服务端保存数据的中间件，需要独立安装

   ```
   npm install express-session --save
   ```

```js
var session = require('express-session')
app.use(session([options]));

// 常用的options
{
    'secret': 'yog', // 签名
    'cookie': {
        'maxAge': 90000 // 因为创建session的同时会创建cookie来保存sessionid，所以options终端恩cookie.maxAge可看做是session的有效时长
    },
    'name': 'session_id' // 在浏览器中生成cookie的名称key, 默认是connect.sid
}


// 使用

// 创建session
req.session.key = value

// 创建多个session
req.session = {
    key1: value1,
    key2: value2
}

// 2. 获取session
var session = req.session // 获取session集合
var value = req.session.key // 获取名称为key的session的值

// 3. 销毁
req.session.destory() // 清空所有session
req.session.key.destory() // 销毁名称为key的session的值
```

## redis

**session存在的问题：** Session用于在服务端保存用户会话状态（如：用户登录信息等），Session 在程序重启、多进程运行、负载均衡、跨域等情况时，会出现Session丢失或多进程、多个负载站点见不能共享的情况

**要解决这些问题：** 需要将Session持久化存储，Redis存储是一个非常不错的Session持久化解决方案

Redis是一个高性能的key-value 数据库

1. 概述

特点

- Redis 支持数据的持久化，可以讲内存中的数据保存在磁盘中，重启时可以再次加载进行使用
- Redis 不仅仅是支持简单的key-value 类型的数据，同时还提供list，set，zset，hash等数据结构的存储
- Redis 支持数据的备份，即master-slave 模式的数据备份

优势

- 性能极高 – Redis能读的速度是110000次/s，写的速度是81000次/s
- 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作
- 原子 – Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性

1. connect-redis
   connect-redis 是一个Redis 版的session存储器，使用node_redis 作为驱动。借助它即可在Express中启用Redis来持久化Session

使用之前先搭建好redis 环境和express应用

参数：
client: 你可以复用现有的redis客户端对象， 由redis.createClient() 创建

host: Redis服务器名

port: Redis服务器端口

socket: Redis服务器的unix_socket

ttl: Redis session TTL 过期时间 （秒）

disableTTL: 禁用设置的 TTL

db: 使用第几个数据库

pass: Redis数据库的密码

prefix: 数据表前辍即schema, 默认为 “sess:”

使用：

```js
// 将 express-session 传给 connect-redis 来启用
// 引入相关模块：
var session = require('express-session');  
var redis = require('redis');  
var RedisStore = require('connect-redis')(session);


// 创建Redis客户端
var redisClient = redis.createClient(6379, '127.0.0.1', {auth_pass: 'password'});


// 设置 Express 的 Session 存储中间件
app.use(session({  
    store:new RedisStore({client: redisClient}),
    secret: 'password',
    resave: false,
    saveUninitialized: false
}))

// 测试
app.use(function (req, res, next) {
  if (!req.session) {
    return next(new Error('error'))
  }
  next()
})
```



此时，session信息就转移到redis数据库了，当应用重启后数据仍然可以通过cookie中的sessionid 获取到，做到数据持久化，提高应用的健壮性。

## Cookie 与 Session 机制

[link](https://zhuanlan.zhihu.com/p/21275207?refer=Anonymous0)

> Cookie通过在客户端记录信息确定身份，Session通过在服务器端记录信息确定用户身份

### cookie 机制

Web应用程序是使用HTTP协议传输数据的。HTTP 协议是无状态的协议，一旦数据交换完毕，客户端与服务器端的连接就会关闭，再次交换数据需要建立新的连接。这就意味着服务器无法从连接上跟踪会话。 (要跟踪该会话，必须引入一种机制)

Cookie是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端颁发一个Cookie。客户端浏览器会把cookie保存起来，当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一桶提交给服务器。服务器检查该cookie，一次来辨认用户状态与权限。服务器还可以根据需要修改Cookie的内容。（Cookie功能需要浏览器的支持，如果不支持或者禁用了，cookie功能能就会失效）

1. cookie 不可跨域性
2. Unicode编码：保存中文 （中文属于Unicode字符，在内存中占4个字节，而英文属于ASCII字符，内存中只占2个字节）使用Unicode字符试需进行编码
3. base64：保存二进制图片
4. cookie的有效期：由maxAge 决定。getMaxAge() 与 setMaxAge(int maxAge) 方法读写maxAge属性
   - maxAge属性为正数，表示该cookie会在maxAge秒之后自动失效。浏览器会将maxAge为正数的cookie持久化，写到对应的Cookie文件中。关闭电脑或浏览器，只要还在maxAge之前，登录网站时cookie仍有效。默认为-1
   - maxAge为负数，表示该cookie仅在本浏览器窗口以及笨窗口打开的子窗口有效，关闭窗口后该cookie即失效，为临时性Cookie，不会持久化，不会写到Cookie文件中
   - maxAge为0，表示删除该Cookie。cookie机制没有提供删除Cookie的方法，因此通过设置该Cookie即时失效实现删除Cookie的效果。
   - 失效的Cookie会被浏览器从Cookie文件或者内存中删除

```js
Cookie cookie = new Cookie('username', 'zhang3'); // 新建Cookie
cookie.setMaxAge(0); // 设置生命周期为0，不能为负数
response.addCookie(cookie); // 输出到客户端
```

```
* response 对象提供的Cookie操作方法只有一个添加操作add(Cookie cookie)
* 要修改Cookie只能使用一个同名的Cookie来覆盖原来的Cookie
* 删除时只需把maxAge修改为0
* 从客户端读取Cookie时，包括maxAge在内的其他属性都是不可读的，也不会被提交。浏览器提交cookie时只会提交name与value属性。maxAge属性只被浏览器用来判断Cookie是否过期
```

1. Cookie的修改、删除

   cookie不提供修改、删除操作。

- 修改某个Cookie，只需新建一个同名的Cookie，添加到response中覆盖原来的Cookie
- 删除某个Cookie，只需新建一个同名的cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie。
- 修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，例如name、path、domain等，都要与原Cookie完全一样。否则，浏览器将视为两个不同的Cookie不予覆盖，导致修改、删除失败。

1. Cookie的域名

   cookie是不可跨域的。

```js
Cookie cookie = new Cookie('time', '20080808'); // 新建Cookie
cookie.setDomain('.hello.com'); // 设置域名
cookie.setPath('/'); // 设置路径
cookie.setMaxAge('MAX_VALUE'); // 设置有效期
response.addCookie(cookie); // 输出到客户端
```

> domain参数必须以点(‘.’)开始。name相同但domain不同的两个Cookie是两个不同的Cookie。如果想要两个域名完全不同的网站共有Cookie，可以生成两个Cookie，domain属性分别为两个域名，输出到客户端

1. Cookie的路径
   path属性决定允许访问Cookie的路径。页面只能获取它属于的Path的Cookie

   ```js
   Cookie cookie = new Cookie("time","20080808"); // 新建Cookie
   
   cookie.setPath("/session/"); // 设置为“/”时允许所有路径使用Cookie。path属性需要使用符号“/”结尾
   
   response.addCookie(cookie); // 输出到客户端
   ```

2. Cookie的安全属性
   如果不希望Cookie在HTTP等非安全协议中传输，可以设置Cookie的secure属性为true。浏览器只会在HTTPS和SSL等安全协议中传输此类Cookie

- cookie.setSecure(true); // 设置安全属性
- secure属性并不能对Cookie内容加密，因而不能保证绝对的安全性。如果需要高安全性，需要在程序中对Cookie内容加密、解密，以防泄密。

1. JavaScript操作Cookie

- document.write(document.cookie);

1. 永久登录

如果用户是在自己家的电脑上上网，登录时就可以记住他的登录信息，下次访问时不需要再次登录，直接访问即可。实现方法是把登录信息如账号、密码等保存在Cookie中，并控制Cookie的有效期，下次访问时再验证Cookie中的登录信息即可。

保存登录信息有多种方案。最直接的是把用户名与密码都保持到Cookie中，下次访问时检查Cookie中的用户名与密码，与数据库比较。这是一种比较危险的选择，一般不把密码等重要信息保存到Cookie中。

还有一种方案是把密码加密后保存到Cookie中，下次访问时解密并与数据库比较。这种方案略微安全一些。如果不希望保存密码，还可以把登录的时间戳保存到Cookie与数据库中，到时只验证用户名与登录时间戳就可以了。

------

### Session 机制

Session是服务器端使用的一种记录客户端状态的机制

客户端浏览器访问服务器时，服务器把客户端信息以某种形式记录在服务器上。就是session。客户端再次访问时只需从Session中查找客户的状态就行。

1. 实现用户登录
   - Session 对象是在客户端第一次请求服务器时创建的。
   - Session也是一种key-value的属性对，通过getAttribute(Stringkey)和setAttribute(String key，Objectvalue)方法读写客户状态信息
   - Session机制决定了当前客户只会获取到自己的Session，而不会获取到别人的Session。各客户的Session也彼此独立，互不可见。
   - Session的使用比Cookie方便，但是过多的Session存储在服务器内存中，会对服务器造成压力。
2. Session的生命周期
   - Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session。
   - 只有访问JSP、Servlet等程序时才会创建Session，只访问HTML、IMAGE等静态资源并不会创建Session。
   - 如果尚未生成Session，也可以使用request.getSession(true)强制生成Session。
   - Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。
3. Session的有效期
   - 为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。
4. Session对浏览器的要求
   - 虽然Session保存在服务器，对客户端是透明的，它的正常运行仍然需要客户端浏览器的支持。这是因为Session需要使用Cookie作为识别标志。
   - HTTP协议是无状态的，Session不能依据HTTP连接来判断是否为同一客户，因此服务器向客户端浏览器发送一个名为JSESSIONID的Cookie，它的值为该Session的id（也就是HttpSession.getId()的返回值）。Session依据该Cookie来识别是否为同一用户。
   - 该Cookie为服务器自动生成的，它的maxAge属性一般为–1，表示仅当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效
   - 新开的浏览器窗口会生成新的Session，但子窗口除外。子窗口会共用父窗口的Session。例如，在链接上右击，在弹出的快捷菜单中选择“在新窗口中打开”时，子窗口便可以访问父窗口的Session
5. URL地址重写
   - URL地址重写是对客户端不支持Cookie的解决方案。
   - URL地址重写的原理是将该用户Session的id信息重写到URL地址中。服务器能够解析重写后的URL获取Session的id。这样即使客户端不支持Cookie，也可以使用Session来记录用户状态。