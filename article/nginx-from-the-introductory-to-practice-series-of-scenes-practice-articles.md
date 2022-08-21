# Nginx从入门到实践系列之场景实践篇

## 静态资源Web服务

### What is static resource?

静态资源其实就是要存在服务器上面的一个实体,你访问的那个资源一定在服务器上面存在,这种称为静态资源.

像`Python/Java/PHP`这些提供动态数据的成为动态资源

|类型|种类|
|:--|:--|
|浏览器端渲染|HTML\CSS\JS|
|图片|JPEG\GIF\PNG|
|视频|ELV\MPEG|
|文件|TXT\等任意下载文件|

### CDN静态资源配置

- [sendfile](http://nginx.org/en/docs/http/ngx_http_core_module.html#sendfile)

内核优化

```bash
Syntax:	    sendfile on | off;
Default:	sendfile off;
Context:	http, server, location, if in location
```

> [--with-file-aio](http://nginx.org/en/docs/http/ngx_http_core_module.html#aio)异步文件读取

- [tcp_nopush](http://nginx.org/en/docs/http/ngx_http_core_module.html#tcp_nopush)

`sendfile`开启的情况下,提高网络包的传输效率

```bash
Syntax:	    tcp_nopush on | off;
Default:	tcp_nopush off;
Context:	http, server, location
```

- [tcp_nodelay](http://nginx.org/en/docs/http/ngx_http_core_module.html#tcp_nodelay)

`keepalive`连接下,提高网络包的传输实时性.

```bash
Syntax:	    tcp_nodelay on | off;
Default:	tcp_nodelay on;
Context:	http, server, location
```

- [文件压缩](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)


把资源进行压缩后在传输

```bash
Syntax:	    gzip on | off;
Default:	gzip off;
Context:	http, server, location, if in location
```

[压缩比](http://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_comp_level)

```bash
Syntax:	    gzip_comp_level level;
Default:	gzip_comp_level 1;
Context:	http, server, location
```

控制压缩[http协议的版本](http://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_http_version)

```bash
Syntax:	    gzip_http_version 1.0 | 1.1;
Default:	gzip_http_version 1.1;
Context:	http, server, location
```

- [预读gzip功能](http://nginx.org/en/docs/http/ngx_http_gzip_static_module.html)

如果资源已经压缩过了,就直接把压缩后的资源返回给浏览器,否则先压缩再返回

- 应用支持[gunzip的压缩](http://nginx.org/en/docs/http/ngx_http_gunzip_module.html)方式

为了解决浏览器不支持`gzip`压缩

#### 效验过期机制

- Expires/Cache-Control(max-age)

> Expires在HTTP1.0版本生效 Cache-Control在1.1版本生效

校验本地缓存是否过期

- Etag

```bash
ETag:W/"58b1dfd7-2bd67"
```

和`Last-Modified`一样,只不过比较的不是时间,而是一串字符串,类似于hash

- `Last-Modified`

```text
Last-Modified:Sat, 25 Feb 2017 19:49:43 GMT  # 时间只能精确到秒
```

请求头中每次携带文件最后一次修改的时间,当浏览器拿到这个事件之后,和本地的文件最比较,如果不是对应的,则缓存失效

### 浏览器缓存场景演示

- [expires](http://nginx.org/en/docs/http/ngx_http_headers_module.html#expires)

添加`Cache-Control/Expires`头

```bash
Syntax:	    expires [modified] time;
            expires epoch | max | off;
Default:	expires off;
Context:	http, server, location, if in location
```

> 有缓存304/无缓存200

### 开启跨站访问

[add_header](http://nginx.org/en/docs/http/ngx_http_headers_module.html#add_header)

```bash
Syntax:	    add_header name value [always];
Default:	—
Context:	http, server, location, if in location
```

```bash
$ cd /usr/share/nginx/html/
$ wget https://www.baidu.com/img/bd_logo1.png
```

本地`html`文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.js"></script>
</head>
<body>
    
</body>
<script>
    $(document).ready(function(){
        $.ajax({
            type:"GET",
            url:"http://127.0.0.1/bd_logo1.png",
            success: function(data){
                console.log("OK");
            },
            error: function(){
                console.log("Fail");
            }
        })
    })
</script>
</html>
```

nginx开启跨越支持

```bash
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    add_header Access-Control-Allow-Origin *;  # 允许所有
    # add_header Access-Control-Allow-Origin http://127.0.0.1;  # 允许个别
    add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,OPTIONS;  # 允许操作的方法
}
$ nginx -s reload
```

### 防盗链

防止资源被盗用

- 基于[ngx_http_referer_module](http://nginx.org/en/docs/http/ngx_http_referer_module.html)防盗链配置模块

```bash
Syntax:	    valid_referers none | blocked | server_names | string ...;
Default:	—
Context:	server, location
```

未开启之前

```bash
$ curl -e "http://www.baidu.com" -I http://127.0.0.1/bd_logo1.png  # -e 伪造`referer`
HTTP/1.1 200 OK
Server: nginx/1.12.1
Date: Wed, 02 Aug 2017 11:36:40 GMT
Content-Type: image/png
Content-Length: 7877
Last-Modified: Wed, 03 Sep 2014 10:00:27 GMT
Connection: keep-alive
ETag: "5406e6bb-1ec5"
Accept-Ranges: bytes
```

开启防盗链

```bash
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    valid_referers none blocked 127.0.0.1;  # 允许谁访问
    if ($invalid_referer) {  # 如果valid_referers为空
        return 403;
    }
}
$ nginx -s reload
```

开启之后

```bash
$ curl -e "http://www.baidu.com" -I http://127.0.0.1/bd_logo1.png
HTTP/1.1 403 Forbidden
Server: nginx/1.12.1
Date: Wed, 02 Aug 2017 11:35:49 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 169
Connection: keep-alive
```
## 代理服务

> http://z00w00.blog.51cto.com/515114/1031287

- 配置语法

> http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass

```bash
Syntax:	proxy_pass URL;
Default:	—
Context:	location, if in location, limit_except
```

通过80端口反向代理到8080

```bash
$ echo 'Hello, Nginx-Proxy!' > /usr/share/nginx/html/index.html
$ cat /etc/nginx/conf.d/proxy.conf 
server {
    listen       80;
    server_name  localhost;
    
    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
$ cat /etc/nginx/conf.d/realserver.conf 
server {
    listen       8080;
    server_name  localhost;
    
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

- 正向代理配置


```bash
# 172.17.0.2
$ cat admin.conf 
server {
    listen       80;
    server_name  localhost;
    
    location / {
        if ( $http_x_forwarded_for !~* "^172\.17\.0\.1" ) {
            return 403;
        }
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

```bash
# 172.17.0.1
$ cat default
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	server_name _;

	location / {
		proxy_pass http://$http_host$request_uri;
	}
}
```

企业常用配置项

```bash
location / {
    proxy_pass http://127.0.0.1;
    proxy_redirect default;
    
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;  # 后端获取真实用户IP

    proxy_connect_timeout 60s;
    proxy_send_timeout 60s;
    proxy_read_timeout 60s;

    proxy_buffer_size 32k;
    proxy_buffering on;
    proxy_buffers 8 128k;
    proxy_busy_buffers_size 256k;
    proxy_max_temp_file_size 256k;
}
```

## 负载均衡调度器SLB服务

- 配置与法

```bash
upstream backend {
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
    server 127.0.0.1:8003;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

- 后端服务器在负载均衡调度中的状态

|状态|描述|
|:--|:--|
|down|当前的Server暂时不参与负载均衡|
|backup|当其他节点不提供服务时,使用预留的备份服务器|
|max_fails|允许请求失败的次数|
|`fail_timeout`|经过`max_fails`失败后,服务暂停的时间|
|max_conns|限制最大的接受的连接数|

- 调度算法

|算法|描述|
|:--|:--|
|轮训|按时间顺序逐一分配到不同的后端服务器|
|加权轮训|weight值越大,分配到的访问几率越高|
|ip_hash|每个请求按访问IP的HASH结果分配,这样来自同一个IP的固定访问一个后端服务器|
|url_hash|按照访问的URL的hash记过来分配请求,是每个URL定向到同一个后端服务器|
|least_conn|最少连接数,那个机器连接数少纠分发|
|hash关键数值|hash自定义的key|

## 动态缓存服务

> http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache

```bash
server {
    upstream backend {
        server 127.0.0.1:8001;
        server 127.0.0.1:8002;
        server 127.0.0.1:8003;
    }
    prooxy_cache_path /tmp/cache levels=1:2 keys_zone=cache:10m max_size=10g inactive=60m user_temp_path=off;
    ......
    location / {
        proxy_cache cache;
        proxy_pass http://backend;
        proxy_cache_valid 200 304 12h;
        proxy_cache_valid any 10m;
        proxy_cache_key $host$uri$is_args$args;
        add_header Nginx-Cache "$upstream_cache_status";
        
        proxy_next_upstream error time invalid_header http_500 http_502 http_504;
        include proxy_params;
    }
}
```