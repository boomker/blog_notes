# Nginx从入门到实践系列之基础篇

## 环境准备

迫于环境的复杂性，我们从`Docker`启动一个`CentOS(version:7)`容器来做当做虚拟机用吧．

```bash
# 映射一个80和443端口出来供我们测试
$ sudo docker run --name nginx -it -p 80:80 -p 443:443 centos /bin/bash
[root@920289892f54 /]#
```

进入容器后进行以下配置

```bash
# 删除默认的yum源
$ rm -f /etc/yum.repos.d/*
# 下载centos7源
$ curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 下载epel源
$ curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
# 清除缓存
$ yum clean all
# 生成缓存
$ yum makecache
# 系统更新
$ yum -y update
# 安装开发工具包
$ yum -y groupinstall "Development Tools"
# 安装工具包
$ yum -y install wget httpd-tools vim tmux net-tools
```

> 如果你使用的不是Docker，请关闭`防火墙`以及`selinux`

## What is Nginx?

[nginx](http://nginx.org/) [engine x] is an HTTP and reverse proxy server, a mail proxy server, and a generic TCP/UDP proxy server, originally written by Igor Sysoev.

## 安装

配置[Nginx YUM](http://nginx.org/en/linux_packages.html#stable)源

```bash
$ vim /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
```

安装Nginx

```bash
$ yum -y install nginx
```

查看`Nginx`版本

```bash
$ nginx -v
nginx version: nginx/1.12.1  # 目前最新稳定版
```

查看Nginx安装时编译的参数

```bash
$ nginx -V
......  # 参数过多就省略了
```

## 基本参数配置

下面的安装参数都是默认从`YUM安装`而来的，如果你是源码安装可能有些不一样

### 目录

```bash
$ rpm -ql nginx  # 通过rpm -ql查看默认Nginx的目录配置
/etc/logrotate.d/nginx  # Nginx日志轮转,用于logrotate服务的日志切割
/etc/nginx  # Nginx主配置文件目录
/etc/nginx/conf.d  # Nginx Server的配置文件目录
/etc/nginx/conf.d/default.conf  # Nginx默认的Server配置问价
/etc/nginx/fastcgi_params  # cgi相关配置文件
/etc/nginx/koi-utf  # 编码转换映射转换文件
/etc/nginx/koi-win  # 编码转换映射转换文件
/etc/nginx/mime.types  # 设置http协议的Content-Type与扩展名对应关系
/etc/nginx/modules  # Nginx模块目录
/etc/nginx/nginx.conf  # Nginx主配置文件
/etc/nginx/scgi_params  # cgi相关配置文件
/etc/nginx/uwsgi_params  # cgi相关配置文件
/etc/nginx/win-utf  # 编码转换映射转换文件
/etc/sysconfig/nginx  # 用于配置系统守护进程管理器管理方式
/etc/sysconfig/nginx-debug  # 用于配置系统守护进程管理器管理方式
/usr/lib/systemd/system/nginx-debug.service  # 用于配置系统守护进程管理器管理方式
/usr/lib/systemd/system/nginx.service  # 用于配置系统守护进程管理器管理方式
/usr/lib64/nginx
/usr/lib64/nginx/modules  # Nginx模块目录
/usr/libexec/initscripts/legacy-actions/nginx
/usr/libexec/initscripts/legacy-actions/nginx/check-reload
/usr/libexec/initscripts/legacy-actions/nginx/upgrade
/usr/sbin/nginx  # Nginx启动管理的终端命令
/usr/sbin/nginx-debug  # Nginx启动管理的终端命令
/usr/share/doc/nginx-1.12.1  # Nginx的手册和帮助文档
/usr/share/doc/nginx-1.12.1/COPYRIGHT  # Nginx的手册和帮助文档
/usr/share/man/man8/nginx.8.gz  # Nginx的手册和帮助文档
/usr/share/nginx  # Nginx静态资源目录
/usr/share/nginx/html  # html页面
/usr/share/nginx/html/50x.html  # 50x错误页面
/usr/share/nginx/html/index.html  # 默认首页
/var/cache/nginx  # Nginx的缓存目录
/var/log/nginx  # Nginx的日志目录
```

### 编译参数

```bash
$ nginx -V
nginx version: nginx/1.12.1
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC)
built with OpenSSL 1.0.1e-fips 11 Feb 2013
TLS SNI support enabled
configure arguments:
--prefix=/etc/nginx  # Nginx配置文件目录
--sbin-path=/usr/sbin/nginx  # Nginx命令行管理工具
--modules-path=/usr/lib64/nginx/modules  # 模块目录
--conf-path=/etc/nginx/nginx.conf  # 默认Nginx配置文件
--error-log-path=/var/log/nginx/error.log  # 错误日志
--http-log-path=/var/log/nginx/access.log  # 访问日志
--pid-path=/var/run/nginx.pid  # PID文件目录
--lock-path=/var/run/nginx.lock  # LOCK文件目录
--http-client-body-temp-path=/var/cache/nginx/client_temp  # 执行对应模块时,Nginx所保留的临时性文件
--http-proxy-temp-path=/var/cache/nginx/proxy_temp  # 执行对应模块时,Nginx所保留的临时性文件
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp  # 执行对应模块时,Nginx所保留的临时性文件
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp  # 执行对应模块时,Nginx所保留的临时性文件
--http-scgi-temp-path=/var/cache/nginx/scgi_temp  # 执行对应模块时,Nginx所保留的临时性文件
--user=nginx  # Nginx进程启动的用户
--group=nginx  # Nginx进程启动的组
--with-compat
--with-file-aio
--with-threads
--with-http_addition_module
--with-http_auth_request_module
--with-http_dav_module
--with-http_flv_module
--with-http_gunzip_module
--with-http_gzip_static_module
--with-http_mp4_module
--with-http_random_index_module
--with-http_realip_module
--with-http_secure_link_module
--with-http_slice_module
--with-http_ssl_module
--with-http_stub_status_module
--with-http_sub_module
--with-http_v2_module
--with-mail
--with-mail_ssl_module
--with-stream
--with-stream_realip_module
--with-stream_ssl_module
--with-stream_ssl_preread_module
--with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64-mtune=generic -fPIC'  # 设置额外的参数将被添加到CFLAGS变量
--with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'  # 设置附加的参数,链接系统库
```

### 配置文件

在CentOS下安装Nginx之后,默认的配置文件内容如下:

```bash
# /etc/nginx/nginx.conf

user  nginx;  # 设置nginx服务的系统使用用户
worker_processes  1;  # 工作进程数

error_log  /var/log/nginx/error.log warn;  # nginx的错误日志
pid        /var/run/nginx.pid;  # nginx服务启动时候的pid


events {  # 事件模块
    worker_connections  1024;  # 每个进程允许最大连接数
    #use epoll;  # 设置nginx使用的内核模型
}


http {  # http模块
    include       /etc/nginx/mime.types;  # 所有支持的Content-Type
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';  # 日志格式

    access_log  /var/log/nginx/access.log  main;  # 访问日志

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;  # 连接超时时间/s

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;   # server,一个http可以包含多个Server
}
```

```bash
# /etc/nginx/conf.d/default.conf
server {  # 一个Server可以包含多个location
    listen       80;  # 监听的端口
    server_name  localhost;  # 主机名,不同主机名就是通过此配置来进行区分

    #charset koi8-r;  # 字符集
    #access_log  /var/log/nginx/host.access.log  main;  # 访问日志目录

    location / {  # 匹配所有的路径
        root   /usr/share/nginx/html;  # 页面存放的目录
        index  index.html index.htm;  # 默认的首页文件
    }

    #error_page  404              /404.html;  # 404页面

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;  # 5xx错误页面
    location = /50x.html {  # 当访问的页面是/50x.html
        root   /usr/share/nginx/html;  # 页面存放路径
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

## 命令行工具

- 检测Nginx配置文件

```bash
$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

- 启动Nginx

```bash
$ nginx
```
- 重载Nginx

```bash
$ nginx -s reload
```

- 停止Nginx

```bash
$ nginx -s stop
```

## 日志

`nginx`的日志([log_format](http://nginx.org/en/docs/http/ngx_http_log_module.html))主要包括了错误日志和访问日志.

- Nginx访问日志的默认定义

```conf
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
```

默认访问日志的配置

|配置|描述|
|:--|:--|
|`$remote_addr`|客户端地址|
|`$remote_user`|客户端请求认证的用户名|
|`$time_local`|Nginx服务器本地的时间|
|`$request`|请求行的信息|
|`$status`|返回的状态码|
|`$body_bytes_sent`|服务端响应给客户端的大小|
|`$http_referer`|当前URL的上一级URL|
|`$http_user_agent`|请求头中的浏览器|
|`$http_x_forwarded_for`|http携带的信息|

通过`curl`指令访问nginx

```bash
$ curl -I 127.0.0.1
HTTP/1.1 200 OK
Server: nginx/1.12.1
Date: Wed, 02 Aug 2017 06:38:00 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 11 Jul 2017 13:50:19 GMT
Connection: keep-alive
ETag: "5964d79b-264"
Accept-Ranges: bytes
```

查看我们刚的访问记录,可以和上面的日志坐下对应.

```bash
$ tail -1 /var/log/nginx/access.log 
127.0.0.1 - - [02/Aug/2017:06:38:00 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.29.0" "-"
```
当然`Nginx`还提供了很多内置的[变量](http://nginx.org/en/docs/http/ngx_http_core_module.html#variables)供我们使用.

### 自定义日志格式

根据上面的变量,自定义我们需要的日志格式,将`nginx.conf`中的`log_format  main`格式改为:

```conf
log_format my_log '$time_local - $server_addr - $server_name - $server_port - $server_protocol';
# 时间 - 服务端地址 - 服务端访问的Server_name - 服务端端口 - 服务端http协议
access_log  /var/log/nginx/access.log  my_log;
```

访问

```bash
$ curl -I 127.0.0.1
HTTP/1.1 200 OK
Server: nginx/1.12.1
Date: Wed, 02 Aug 2017 06:58:16 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 11 Jul 2017 13:50:19 GMT
Connection: keep-alive
ETag: "5964d79b-264"
Accept-Ranges: bytes
```

查看日志记录

```bash
$ tail -1 /var/log/nginx/access.log 
02/Aug/2017:06:58:16 +0000 - 127.0.0.1 - localhost - 80 - HTTP/1.1
```

## 模块

介绍一个内置的模块,虽然不常用.

### `--with-http_stub_status_module`

显示`Nginx`客户端的链接状态

> http://nginx.org/en/docs/http/ngx_http_stub_status_module.html

```bash
$ vim /etc/nginx/conf.d/default.conf
# 在server中加入下面的location
location /status {
    stub_status;
}
# 生效Nginx配置
$ nginx -s reload
```

访问

```bash
# curl 127.0.0.1/status
Active connections: 1
server accepts handled requests
 7 7 3 
Reading: 0 Writing: 1 Waiting: 0
```

|状态|描述|
|:--|:--|
|`Active connections`|The current number of active client connections including Waiting connections.|
|`accepts`|The total number of accepted client connections.|
|`handled`|The total number of handled connections. Generally, the parameter value is the same as accepts unless some resource limits have been reached (for example, the worker_connections limit).|
|`requests`|The total number of client requests.|
|`Reading`|The current number of connections where nginx is reading the request header.|
|`Writing`|The current number of connections where nginx is writing the response back to the client.|
|`Waiting`|The current number of idle client connections waiting for a request.|

### `--with-http_random_index_module`

在目录中选择一个随机文件作为默认主页

> http://nginx.org/en/docs/http/ngx_http_random_index_module.html

```bash
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    random_index on;
}
$ nginx -s reload
$ rm -f /usr/share/nginx/html/*
$ echo 1 > /usr/share/nginx/html/1.html
$ echo 2 > /usr/share/nginx/html/2.html
$ echo 3 > /usr/share/nginx/html/3.html
$ curl 127.0.0.1
1
$ curl 127.0.0.1
1
$ curl 127.0.0.1
3
$ curl 127.0.0.1
2
```

> 如果是隐藏文件则不会被nginx当做主页

### `--with-http_sub_module`

HTTP Resp内容替换

> http://nginx.org/en/docs/http/ngx_http_sub_module.html

```bash
$ vim /usr/share/nginx/html/index.html
welcome to ansheng!
$ curl 127.0.0.1
welcome to ansheng!
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    sub_filter 'welcome' 'Welcome';
    # sub_filter_once off;  # 默认只替换匹配到的第一个,改为off全局替换
}
$ nginx -s reload
$ curl 127.0.0.1
Welcome to ansheng!  # 替换的是第一个匹配到的字符串
```

## 请求限制

```bash
$ echo 'Hello, World!' > /usr/share/nginx/html/index.html
```

### 连接频率限制 - limit_conn_module

> http://nginx.org/en/docs/http/ngx_http_limit_conn_module.html

```bash
$ vim /etc/nginx/nginx.conf
http {
    limit_conn_zone $binary_remote_addr zone=addr:10m;
    # $binary_remote_addr=key  addr=name 10m=size
}
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    limit_conn addr 1;  # 同一时刻只允许一个IP请求
}
```

### 请求频率限制 - limit_req_module

> http://nginx.org/en/docs/http/ngx_http_limit_req_module.html

```bash
$ vim /etc/nginx/nginx.conf
http {
    limit_req_zone $binary_remote_addr zone=req_zone:10m rate=1r/s;
    # 同一个IP地址每秒只能一个请求
}
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    limit_req zone=req_zone burst=3;
    # burst=3 延迟响应,这一秒的三个请求留给下一秒处理
}
```

## 访问控制

### 基于IP的访问控制([http_access_module](http://nginx.org/en/docs/http/ngx_http_access_module.html))

```bash
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    deny 127.0.0.1;  # 拒绝127.0.0.1访问
    allow all;  # 其他都允许
}
```

```bash
# 127.0.0.1拒绝
$ curl -I 127.0.0.1
HTTP/1.1 403 Forbidden
# 17.17.0.2允许
$ curl -I 172.17.0.2
HTTP/1.1 200 OK
```

`http_access_module`只会根据IP(remote_addr)来进行区分,如果要解决这个问题:

1. 采用别的HTTP头信息控制访问,如:`HTTP_X_FORWARD_FOR`
2. 结合geo模块作
3. 采用HTTP自定义变量传递

### 基于用户的信任登录([http_auth_basic_module](http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html))

```bash
$ htpasswd -c ./auth_conf ansheng  # 生成密码文件
New password: # 密码
Re-type new password: # 确认密码
Adding password for user ansheng
$ mv ./auth_conf /etc/nginx/
$ vim /etc/nginx/conf.d/default.conf
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    auth_basic "Auth access test!input your password!";
    auth_basic_user_file /etc/nginx/auth_conf;
}
$ nginx -s reload
```

- 局限性

1. 用户信息依赖文件方式
2. 操作管理机械,效率低下

- 解决方案

1. Nginx解决LUA实现高效验证
2. Nginx和LDAP打通,利用nginx-auth-ldap`模块