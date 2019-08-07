---
title: nginx-configure-explanation
date: 2018-11-02 11:08:04
tags: nginx
---

## nginx 配置文件nginx.conf 详解

> #nginx 进程，一般设置为和cpu核数一样

```
worker_process 4;
```

> \#错误日志存放目录

```
error_log   /logs/error.log   crit;
```

> \#运行用户，默认即是nginx,可不设置

```
user nginx;
```

> \#进程pid存放位置

```
pid     /logs/nginx.pid;
```

> \#最大文件打开书（连接），可设置为系统优化后的ulimit-HSn的结果

```
worker_rlimit_nofile 51200;
```

> \#cpu亲和力配置，让不同的进程使用不同的cpu

```
worker_cpu_affinity 0001 0010 0100 1000 0001 00100100 1000;
```

> \#工作模式及连接数上限

```shell
events 
{
    use epoll;       #epoll是多路复用IO(I/O Multiplexing)中的一种方式,但是仅用于linux2.6以上内核,可以大大提高nginx的性能
    worker_connections 1024;  #;单个后台worker process进程的最大并发链接数
}
###################################################

http

{
    include mime.types; #文件扩展名与类型映射表
    default_type application/octet-stream; #默认文件类型
```

> \#limit 模块，可防范一定量的DDOS攻击
>
> \#用来存储session会话的状态，如下是session分配一个名为one的10M的内存存储区，限制了每秒只接收一个ip的一次请求 1r/s

```shell
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
limit_conn_zone $binary_remote_addr zone=addr:10m;
```

> \#设定请求缓存

```shell
server_names_hash_bucket_size 128;

client_header_buffer_size 512k;

large_client_header_buffers 4512k;

client_max_body-size 100m;
```

> \#隐藏响应header和错误通知中的版本号

```shell
server_tokens off;
```

> \#开启高效传输模式

```shell
sendfile on;
```

> \#激活tcp_nopush 参数可以允许把httpresponse header 和文件的开始放在一个文件里发布，积极的作用是减少网络报文段的数量

```shell
tcp_nopush on;
```

> \#激活
> tcp_nodelay, 内核会等待将更多的字节组成一个数据包，从而提高I/O性能

```shell
tcp_nodelay on;
```

> \#FastCGI 相关参数：为了改善网站性能：减少资源占用，提高访问速度

```shell
fastcgi_connect_timeout 300;
fastcgi_send_timeout 300;
fastcgi_read_timeout 300;
fastcgi_buffer_size 64k;
fastcgi_buffers 4 64k;
fastcgi_busy_buffers_size 128k;
fastcgi_temp_file_write_size 128k;
```

> \#连接超时时间，单位为秒

 keepalive_timeout 60;

> \#开启gzip 压缩功能

```shell
gzip on;
```

> \#设置允许压缩的页面最小字节数，页面字节数从header头的Content-Length 中获取。默认值是0，表示不管页面多大都进行压缩。建议设置成大于1K。如果小于1K可能会越压越大

```shell
gzip_min_length 1k;
```

> \#

```shell
}
```



```shell
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;

#error_log  logs/error.log  notice;

#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    resolver 172.22.1.253;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8086;
        server_name  localhost;

        #charset koi8-r;

        access_log  lihuan.access.log;

        location / {
	    proxy_pass http://family.baidu.com$request_uri;
        }
        #error_page  404              /404.html;

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    server {
	listen       8087;
	server_name  127.0.0.1;

	location / {
	    proxy_pass http://127.0.0.1:8080;
	}
	# location /api {
	#	rewrite ^.+api/?(.*)$ /$1 break;
	#	proxy_pass http://api.b.com:8080/api/$1
	# }

        location ~ /api/ {
	    proxy_pass http://meeting-bot.dev.weiyun.baidu.com;
	}

    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

    include servers/*;
}
```