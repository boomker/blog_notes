#  Nginx从入门到实践系列之深度学习篇

## 动静分离

> 通过中间件(Nginx)将动态请求和静态请求进行分离

所谓动静分离其实就是把静态资源文件交给`Nginx`处理,动态请求通过`Nginx`转发给实际提供服务的后端程序处理,这样就可以减少不必要的请求消耗,减少请求延时.

- Nginx配置(`/etc/nginx/conf.d/default.conf`)

```
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:5000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 43200000;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_buffering off;
    }

}
```

启动nginx

```bash
$ nginx
```

- 后端(`tornado`)

安装软件包

```bash
$ yum -y install python-pip
$ pip install -U pip
$ pip install tornado
```

程序文件`/root/app.py`

```python
import tornado.ioloop
import tornado.web
from random import randint

class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write('Hello {number}!'.format(number=randint(1,10000)))

application = tornado.web.Application([
    (r'/', MainHandler),
])

if __name__ == "__main__":
    application.listen(5000)
    tornado.ioloop.IOLoop.instance().start()
```

- 前端页面

下载静态资源图片

```bash
$ cd /usr/share/nginx/html/
$ wget http://nginx.org/nginx.png
```

首页`html`文件`/usr/share/nginx/html/index.html`

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.js"></script>
    <title>Document</title>
</head>

<body>
    <h1>动静分离</h1>
    <img src="./nginx.png" alt="">
    <h1 id="get_data"></h1>
</body>
<script>
    $.ajax({
        type: "GET",
        url: "http://127.0.0.1/api/",
        success: function (data) {
            $("#get_data").html(data)
        },
        error: function () {
            alert("Fail!")
        }
    })

</script>

</html>
```

- 启动后端

```bash
$ python /root/app.py
```

- 测试

首先打开浏览器访问`http://127.0.0.1/`,你会发现有一个数字每次刷新都会变动,这就是后端返回的数据

然后关闭后端程序,再次刷新会提示`Fail!`

## rewrite规则

> 实现url重写以及重定向,依赖于正则表达式

- 配置语法

> http://nginx.org/en/docs/http/ngx_http_rewrite_module.html

把所有的请求都重定向到`/pages/maintain.html`页面并且退出

```
rewrite ^(.*)$ /pages/maintain.html break;
```

- 正则表达式

|正则|描述|
|:--|:--|
|`.`|匹配除换行符以外的任意字符|
|`?`|重复0次或多次|
|`+`|重复1次或多次|
|`*`|最少连接数,哪个机器连接数少就分发|
|`\d`|匹配数字|
|`^`|匹配字符串的开始|
|`$`|匹配字符串的结束|
|`{n}`|重复n次|
|`{n,}`|重复n次或更多次|
|`[c]`|匹配单个字符串c|
|`[a-z]`|匹配a-z小写字母的任意一个|
|`\`|转义字符|
|`()`|用于匹配括号之间的内容,通过$1,$2调用|

以交互式的方式测试正则表达式

> `centos`下面没找到这个包,Ubuntu请安装`sudo apt install pcre2-utils`

```bash
~$ pcre2test
PCRE2 version 10.22 2016-07-29
  re> /(\d+)\.(\d+)\.(\d+)\.(\d+)/
data> 192.168.1.1
 0: 192.168.1.1  # 全部内容
 1: 192  # 第一个括号
 2: 168  # 第二个括号
 3: 1  # 第三个括号
 4: 1  # 第四个括号
data> asdasdasd
No match  # 正则表达式没有匹配到
```

- flag

终止对`rewrite`指令的进一步处理

|标记|描述|
|:--|:--|
|`last`|停止rewrite检测|
|`break`|停止rewrite检测|
|`redirect`|返回302临时重定向,地址栏会显示跳转后的地址|
|`permanent`|返回301永久重定向,地址栏会显示跳转后的地址|

- `last`与`break`的区别演示

```bash
$ vim /etc/nginx/conf.d/default.conf
server {
    listen       80;
    server_name  localhost;

    root   /usr/share/nginx/html;

    location ~ ^/break {
        # 当匹配到/break这个路径的时候
        # 会在/usr/share/nginx/html目录下寻找是否有这个路径
        rewrite ^/break /test/ break;
    }

    location ~ ^/last {
        # 当匹配到/last这个路径的时候
        # 新建一个请求,路径是以test结尾的,所以就被下面的location处理了
        rewrite ^/last /test/ last;
    }

    location /test/ {
        default_type application/json;
        return 200 '{"status":"success"}';
    }
}
$ nginx -s reload
```

依次访问

```bash
$ curl http://127.0.0.1/test/
{"status":"success"}
$ curl http://127.0.0.1/last/
{"status":"success"}
$ curl http://127.0.0.1/break/
<html>
<head><title>404 Not Found</title></head>
<body bgcolor="white">
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.12.1</center>
</body>
</html>
```

- `redirect`和`permanent`区别

```bash
$ vim /etc/nginx/conf.d/default.conf
server {
    listen       80;
    server_name  localhost;
    access_log  /var/log/nginx/host.access.log  main;
    root   /usr/share/nginx/html;

    location ~ ^/redirect {
        rewrite ^/redirect /test/ redirect;
    }

    location ~ ^/permanent {
        rewrite ^/permanent /test/ permanent;
    }

    location /test/ {
        default_type application/json;
        return 200 '{"status":"success"}';
    }
}
$ nginx -s reload
```

`curl`演示

```bash
# 请空日志
$ > /var/log/nginx/host.access.log
# 访问
$ curl -I http://127.0.0.1/redirect/  # 客户端不会保留跳转信息,每次都会先从服务器请求道跳转的地址在跳转
HTTP/1.1 302 Moved Temporarily
Server: nginx/1.12.1
Date: Fri, 04 Aug 2017 04:10:01 GMT
Content-Type: text/html
Content-Length: 161
Location: http://127.0.0.1/test/
Connection: keep-alive
# 查看日志
$ tail -5 /var/log/nginx/host.access.log
127.0.0.1 - - [04/Aug/2017:04:13:16 +0000] "HEAD /redirect/ HTTP/1
.1" 302 0 "-" "curl/7.29.0" "-"

######################

# 清空日志
$ > /var/log/nginx/host.access.log
# 访问
$ curl -I http://127.0.0.1/permanent/  # 客户端会一直保留跳转的信息,只要不清理缓存
HTTP/1.1 301 Moved Permanently
Server: nginx/1.12.1Date: Fri, 04 Aug 2017 04:14:15 GMT
Content-Type: text/html
Content-Length: 185
Location: http://127.0.0.1/test/
Connection: keep-alive
# 查看日志
$ tail -5 /var/log/nginx/host.access.log
127.0.0.1 - - [04/Aug/2017:04:14:15 +0000] "HEAD /permanent/ HTTP/
1.1" 301 0 "-" "curl/7.29.0" "-"
```

- 场景

多路径的URL改写,便于SEO收录

```bash
location / {
    rewrite ^/course-(\d+)-(\d+)-(\d+)\.html$ /course/$1/$2/course_$3.html break;
    # /course-1-1-1.html to /course/1/1/course_1.html
    if ($http_user_agent ~* Chrome) {  # 如果浏览器是Chrome
        # 且是/nginx路径开头,然后就跳转到http://coding.imooc.com/class/121.html
        rewrite ^/nginx http://coding.imooc.com/class/121.html redirect;
    }

    if ( !-f $request_filename ) {  # 如果文件名路径不存在
        # 将请求匹配到的所有内容转发到baidu
        rewrite ^/(.*) http://www.baidu.com/$1 redirect;
    }
}
```

- 规则优先级

`server` > `location` > 执行选定的location中的rewrite

- 优雅的规则

`nginx.org`跳转到`www.nginx.org`

```bash
server {
    listen 80;
    server_name nginx.org;
    rewrite ^ http://www.nginx.org$request_url?;
}
server {
    listen 80;
    server_name www.nginx.org;
}
```

## 进阶高级模块

介绍一些最新或者理解难度稍高的`Nginx`模块

### `secure_link`模块实现请求资源验证

> http://nginx.org/en/docs/http/ngx_http_secure_link_module.html

1. 制定并允许检查请求的链接真实性以及保护资源免遭未经授权的访问;
2. 限制链接生效周期;

- 原理

拿下载文件为实例:

1. 客户端点击下载按钮向服务端获取下载地址;
2. 服务端生成下载地址(加密后的`/donload?md5=MD5&expires=21462xxxxx`)并返回客户端;
3. 客户端拿到下载地址到服务端下载资源;
4. 服务端验证下载地址是否合法,如果合法就把资源传输给客户端;

- 实例

创建下载的文件

```bash
$ echo 'Hello, ansheng!' > /usr/share/nginx/html/download/hello.txt
$ cat /usr/share/nginx/html/download/hello.txt
Hello, ansheng!
```

配置`Nginx`

```bash
$ vim /etc/nginx/conf.d/default.conf
......
    server {
        listen       80;
        server_name  localhost;
        root   /usr/share/nginx/html;

        location / {
            secure_link $arg_md5,$arg_expires;  # 获取参数
            secure_link_md5 "$secure_link_expires$uri ansheng";  # 通过哪些值进行加密
            # 当获取的加密值和在服务端生成后的加密至匹配成功`secure_link`变量值就会有内容,如果变量值是空或0则拒绝下载
            if ($secure_link = "") {
                return 403;
            }

            if ($secure_link = "0") {
                return 410;
            }

        }
    }
......
```
胜服务端生成下载地址所需要的参数
```bash
$ vim md5url.sh
#!/bin/bash
ServerName="localhost"
DownloadFile="/download/hello.txt"
Time_num=$(date -d '2018-08-01 00:00:00' +%s)
secret_num="secret"

res=$(echo -n "${Time_num}${DownloadFile} ${secret_num}" | openssl md5 -binary | openssl base64 | tr +/ -_ | tr -d =)
echo "http://${ServerName}${DownloadFile}?md5=$(res)&expires=${Time_num}"
$ bash md5url.sh  # 生成下载的URL
http://localhost/download/hello.txt?md5=3fKL59pM5Csnvh3l-mDmsA&expires=1533081600
```

- 测试

正常情况下

```bash
$ curl http://localhost/download/hello.txt?md5=3fKL59pM5Csnvh3l-mDmsA\&expires=1533081600
Hello, ansheng!
```
随便修改了`md5`的值
```bash
$ curl http://localhost/download/hello.txt?md5=3fKL59pM5Csnvh3l-mDmsAxxxxxxxxxxxx\&expires=1533081600
<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.12.1</center>
</body>
</html>
```

> 由于url中有`&`，必须对`&`进行下转义才能使用

### Geoip读取地域信息模块

基于IP地址匹配`MaxMind GeoIP`二进制文件,读取IP所在地域信息.

> 编译安装时指定`--with-http_geoip_module`模块

- 场景

1. 区别国内外HTTP访问规则
2. 区别国内城市地域HTTP访问规则


- 下载Geo二进制文件

```bash
$ mkdir /etc/nginx/geoip/
$ cd /etc/nginx/geoip/
$ wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz
$ wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
$ gunzip GeoIP.dat.gz
$ gunzip GeoLiteCity.dat.gz
```

```
$ vim /etc/nginx/conf.d/default.conf
geoip_country /etc/nginx/geoip/GeoIP.dat;
geoip_city /etc/nginx/geoip/GeoLiteCity.dat;

server {
    listen       80;
    server_name  localhost;

    location / {
        if ($geoip_country_code != CN) {
            return 403;
        }
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    location /myip/ {
        default_type text/plain;
        return 200 "$remote_addr $geoip_country_name $geoip_country_code $geoip_city";
    }

}
```

## HTTPS服务

### 生成自签证书

- 生成KEY秘钥

```bash
$ cd /etc/nginx/
$ mkdir ssl
$ cd ssl
$ openssl genrsa -idea -out ssl.key 1024
# 密码
# 确认密码
```

- 生成证书签名请求文件(csr)文件

```bash
$ openssl req -new -key ssl.key -out ssl.csr
# 密码
..... # 一路回车
```

- 生成证书签名文件(CA文件)

```bash
$ openssl x509 -req -days 3650 -in ssl.csr -signkey ssl.key -out ssl.crt
# 密码
```

- 配置https服务

```bash
$ vim /etc/nginx/conf.d/default.conf
server {
    listen       443;
    server_name  localhost;
    ssl on;
    ssl_certificate /etc/nginx/ssl/ssl.crt;
    ssl_certificate_key /etc/nginx/ssl/ssl.key;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
$ nginx -s reload
Enter PEM pass phrase:
```

打开浏览器访问`https://127.0.0.1/`并信任证书

### 构建一个满足苹果要求的HTTPS后台服务

- 苹果对证书的要求

1. 服务器所有的连接使用`TLS1.2`以上的版本(`openssl 1.0.2`);
2. HTTPS证书必须使用`SHA256`以上哈希算法签名;
3. HTTPS证书必须使用`RSA 2048`位或`ECC 256`位以上公钥算法;
4. 使用向前加密技术;

- 升级openssl

```bash
$ wget https://www.openssl.org/source/openssl-1.0.2k.tar.gz
$ tar xf openssl-1.0.2k.tar.gz
$ cd openssl-1.0.2k
$ ./config --prefix=/usr/local/openssl
$ make && make install

$ mv /usr/bin/openssl /usr/bin/openssl.OFF
$ ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
$ echo '/usr/local/openssl/lib' >> /etc/ld.so.conf
$ ldconfig
$ openssl version
OpenSSL 1.0.2k  26 Jan 2017
```

- 根据KEY生成证书

```bash
$ cd /etc/nginx/ssl/
$ openssl req -days 36500 -x509 -sha256 -nodes -newkeyrsa:2048 -keyout ssl.key -out ssl_apple.crt
$ sed -i 's#ssl.crt#ssl_apple.crt#g' /etc/nginx/conf.d/default.conf
$ nginx -s reload
```

### HTTPS服务优化

- 激活`keepalive`长连接

```
keepalive_timeout  100;
```

- 设置`ssl session`缓存;

```
ssl_session_cache   shared:SSL:10m;
ssl_session_timeout 10m;
```

## Nginx与Lua开发

`Lua`是一个简介,轻量,可扩展的脚本语言.

- Nginx+Lua优势

充分的结合`Nginx`的并发处理`epoll`优势和`LUA`的轻量实现简单的功能且高并发的场景.

### Lua基础语法

- 安装Lua

```bash
$ yum -y install lua
```

- 进入Lua交互式解释器

```bash
$ lua
Lua 5.1.4  Copyright (C) 1994-2008 Lua.org, PUC-Rio
>
```

- 输出

```bash
> print('Hello, World!')
Hello, World!
```

- 运行脚本

```bash
$ vim hell.lua
#!/usr/bin/lua
print("I'm Lua!")
$ lua hell.lua  # 执行脚本
I'm Lua!
```

- 注释

```lua
-- 行注释

--[[
    块注释    
--]]
```

- 变量

```lua
a = 'alo\n123'
a = "alo\n123"
a = '\97lo\10\04923'
a = [[alo
123]]
```

> 布尔类型只有nil和false,数字0或者空字符串都表示true,Lua中的变量如果没有特殊说明,全是全局变量

- while循环

```lua
sum = 0
num = 1
while num <= 100 do
    sum = sum + num
    num = num + 1
end
print("sum =",sum)
```

> Lua没有++或是+=这样的操作

- for循环

```lua
sum = 0
for i = 1, 100 do
    sum = sum + i
end
print("sum =", sum)
```

- if-else判断

```lua
age = 40
sex = "Male"
if age == 40 and sex == "Male" then
    print("大于40男人")
elseif age > 60 and sex ~= "Female" then
    print("非女人而且大于60")
else
    local age = io.read()
    print("Your age is"..age)
end
```

1. `~=`是不等于;
2. 字符串的拼接操作符`..`;
3. `io`库的分别从`stdin`和`stdout`读写的`read`和`write`函数;

### Nginx+Lua的开发环境

- 安装LuaJIT

```bash
$ wget http://luajit.org/download/LuaJIT-2.0.2.tar.gz
$ tar xf LuaJIT-2.0.2.tar.gz
$ cd LuaJIT-2.0.2
$ make install PREFIX=/usr/local/LuaJIT
$ export LUAJIT_LIB=/usr/local/LuaJIT/lib
$ export LUAJIT_INC=/usr/local/LuaJIT/include/luajit-2.0
$ luajit -v  # 查看版本
LuaJIT 2.0.2 -- Copyright (C) 2005-2013 Mike Pall. http://luajit.org/
```

- `ngx_devel_kit`和`lua-nginx-module`

```bash
$ wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
$ wget https://github.com/openresty/lua-nginx-module/archive/v0.10.9rc7.tar.gz
$ tar xf v0.3.0.tar.gz
$ tar xf v0.10.9rc7.tar.gz
```

- 重新编译nginx

```bash
$ nginx -s stop
$ yum -y remove nginx
$ yum -y install pcre-devel openssl-devel
$ wget http://nginx.org/download/nginx-1.12.1.tar.gz
$ tar xf nginx-1.12.1.tar.gz
$ cd nginx-1.12.1
$ ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie' --add-module=/root/ngx_devel_kit-0.3.0/ --add-module=/root/lua-nginx-module-0.10.9rc7
$ make -j 4 && make install
$ ln -s /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2
$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### Nginx与Lua环境


- Nginx调用Lua模块指令

`Nginx`的可插拔模块化加载执行,共11个处理阶段.

|指令|描述|
|:--|:--|
|`set_by_lua`或`set_by_lua_file`|设置Nginx变量,可以实现复杂的赋值逻辑|
|`access_by_lua`或`access_by_lua_file`|请求访问阶段处理,用于访问控制|
|`content_by_lua`或`content_by_lua_file`|内容处理器,接受请求处理并输出响应|

- Nginx Lua API

|API|描述|
|:--|:--|
|`ngx.va`|Nginx变量|
|`ngx.req.get_headers`|获取请求头|
|`ngx.req.get_uri_args`|获取url请求参数|
|`ngx.redirect`|重定向|
|`ngx.print`|输出响应内容体|
|`ngx.say`|通ngx.print,但是会最后输出一个换行符|
|`ngx.headers`|输出响应头|
.....

### 用Nginx与Lua实现代码的灰度发布

按照一定的关系区别,分部分的代码进行上线,使代码的发布能平滑过度上线.

根据用户的cookie信息或者IP地址来区分用户,

- 流程

1. 后端有两台服务器,一台正式环境一台开发环境;
2. `127.0.0.1`的用户访问开发环境;
3. 其他用户访问正式环境;
4. 通过`Nginx+Lua`来进行控制;
5. Lua把用户的IP地址存储在`Memcache`中;

- 安装Memcache

```bash
$ yum -y install memcached
# 启动
$ memcached -p11211 -u nobody -d
```

- 在memcache中添加IP

```bash
$ telnet 127.0.0.1 11211
$ set 127.0.0.1 0 0 1
$ 1
STORED
$ get 127.0.0.1
VALUE 127.0.0.1 0 1
1
END
```

> yum -y install telnet

- 配置两台后端Server

```bash
$ vim /etc/nginx/nginx.conf
worker_processes  auto;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    include /etc/nginx/conf.d/*.conf;
}
$ vim /etc/nginx/conf.d/server1.conf
server {
    listen       5000;
    server_name  localhost;

    location / {
        default_type application/json;
        return 200 '{"env":"product"}';
    }
}
$ vim /etc/nginx/conf.d/server2.conf
server {
    listen       6000;
    server_name  localhost;

    location / {
        default_type application/json;
        return 200 '{"dev":"developer"}';
    }
}
```

访问测试

```bash
$ curl 127.0.0.1:5000
{"env":"product"}
$ curl 127.0.0.1:6000
curl 127.0.0.1:6000
```

- 安装Lua连接Memcache的模块

```bash
$ wget https://github.com/agentzh/lua-resty-memcached/archive/v0.11.tar.gz
$ tar xf v0.11.tar.gz
$ cp -r lua-resty-memcached-0.11/lib/resty/ /usr/local/share/lua/5.1/
```

- 配置nginx

```bash
$ vim /etc/nginx/conf.d/default.conf
server {
    listen       80;
    server_name  localhost;

    location /hello {
        default_type 'text/plain';
        content_by_lua 'ngx.say("hello, lua")';
    }

    location / {
        default_type "text/html"; 
        content_by_lua_file /root/dep.lua;
    }

    location @server{
        proxy_pass http://127.0.0.1:5000;
    }

    location @server_dev{
        proxy_pass http://127.0.0.1:6000;
    }
}
$ nginx  -s reload
```

- lua脚本

> /root/dep.lua

```lua
#!/usr/bin/lua

-- 获取客户端的X-Real-IP(自定义变量)
clientIP = ngx.req.get_headers()["X-Real-IP"]

-- 如果没有取到IP
if clientIP == nil then
    -- 就取x_forwarded_for IP
    clientIP = ngx.req.get_headers()["x_forwarded_for"]
end

-- 当x_forwarded_for也为空的时
if clientIP == nil then
    -- 获取remote_addr的IP
    clientIP = ngx.var.remote_addr
end
    -- 调用加载memcache的库
    local memcached = require "resty.memcached"
    -- 实例化对象
    local memc,err = memcached:now()
    if not memc then -- 如果没有获取到对象
        ngx.say("Failed to instantiate memc: ",err)
        return
    end
    local ok, err = memc:connect("127.0.0.1", 11211)  -- 连接memcached
    if not ok then  -- 如果连接失败
        ngx.say('Failed to connect: ',err)
        return
    end
    local res, flags, err = memc:get(clientIP) -- 在memcache中获取客户端IP,IP为KEY
    ngx.say("value key: ",res,clientIP)
    if err then
        ngx.say('Failed to get clientIP ',err)
        return
    end
    if res == "1" then  -- 如果获取到了,就访问测试环境
        ngx.exec("@server_dev")
        return
    end
    ngx.exec("@server")  -- 其他的都走正式环境
```
