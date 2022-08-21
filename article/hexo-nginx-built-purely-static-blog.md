# 晒晒我的Hexo+Nginx+SSL搭建的纯静态博客

服务器多,域名也多,天天都在弄环境,服务器用阿里云的机器`1H`,`1G`,`5M带宽`,域名也在阿里来注册的,已准备转移至国外,毕竟国内太坑.

## 环境初始化

先来看看我的云服务器都是什么环境把
```bash
[root@ansheng ~]# hostname
ansheng.me
[root@ansheng ~]# whoami
root
```
系统我用的是目前最新的`CentOS 7.2`
```bash
[root@ansheng ~]# cat /etc/redhat-release
CentOS Linux release 7.2.1511 (Core)
[root@ansheng ~]# uname -a
Linux ansheng.me 3.10.0-327.el7.x86_64 #1 SMP Thu Nov 19 22:10:57 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```
已经关闭`SElinux`与`firewall`
```bash
[root@ansheng ~]# getenforce
Disabled
[root@ansheng ~]# systemctl status firewall
● firewall.service
   Loaded: not-found (Reason: No such file or directory)
   Active: inactive (dead)
```

更新系统及安装基础环境包

貌似默认阿里在`CentOS 7.2`系统里面并没有放`repo`文件
```bash
[root@ansheng ~]# ls /etc/yum.repos.d/
```
既然如此我们就自己去下载`repo`文件把
```bash
[root@ansheng ~]# wget
wget：未指定 URL
用法： wget [选项]... [URL]...

请尝试使用“wget --help”查看更多的选项。
```
还好默认就已经把`wget`命令装上了,要不然又要自己去手动安装`wget`了,如果没有安装青石用以下命令安装`wget`命令

```bash
[root@ansheng ~]# rpm -ivh http://mirrors.aliyun.com/centos/7.2.1511/os/x86_64/Packages/wget-1.14-10.el7_0.1.x86_64.rpm
```
下载`repo`文件
```bash
[root@ansheng ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
[root@ansheng ~]# wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```

清除旧缓存更新新缓存

```bash
[root@ansheng ~]# yum clean all
[root@ansheng ~]# yum makecache
```

更新系统到最新版

```bash
[root@ansheng ~]# yum update -y
```

安装必要的基础软件包

```bash
[root@ansheng ~]# yum -y groupinstall "Compatibility libraries" "Development tools" "debugging Tools" "Dial-up Networking Support"
```

重启系统

```bash
[root@ansheng ~]# reboot
```

## 编译安装Nginx

下载Nginx

`Nginx`依旧采用目前最新的稳定版本`	nginx-1.10.1`
```bash
[root@ansheng ~]# wget http://nginx.org/download/nginx-1.10.1.tar.gz
[root@ansheng ~]# tar xf nginx-1.10.1.tar.gz
[root@ansheng ~]# cd nginx-1.10.1
```

创建用户及用户组

```bash
[root@ansheng nginx-1.10.1]# useradd -s /sbin/nologin -M nginx
[root@ansheng nginx-1.10.1]# id nginx
uid=1000(nginx) gid=1000(nginx) 组=1000(nginx)
```

安装编译所需要的软件包

```bash
[root@ansheng nginx-1.10.1]# yum -y install openssl openssl-devel pcre pcre-devel perl-ExtUtils-Embed libxslt libxslt-devel gd gd-devel GeoIP GeoIP-devel
```

编译安装Nginx

```bash
[root@ansheng nginx-1.10.1]# ./configure \
--prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_sub_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_mp4_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_stub_status_module \
--with-http_auth_request_module \
--with-http_xslt_module=dynamic \
--with-http_image_filter_module=dynamic \
--with-http_geoip_module=dynamic \
--with-http_perl_module=dynamic \
--with-threads \
--with-stream \
--with-stream_ssl_module \
--with-http_slice_module \
--with-mail \
--with-mail_ssl_module \
--with-file-aio \
--with-http_v2_module \
--with-ipv6
[root@ansheng nginx-1.10.1]# make
[root@ansheng nginx-1.10.1]# make install
```

## 安装Hexo

`Hexo`是用`NodeJS`写的,文章用`Markdown`语法写,`Hexo`会把`.md`的文件生成`HTML`文件以便于浏览,因为是静态文件,所以这也是为什么很多人使用国外的VPS来做博客访问速度依旧这么快的原因.

安装git

```bash
[root@ansheng nginx-1.10.1]# cd
[root@ansheng ~]# git --version
git version 1.8.3.1
```
`CentOS 7.2`默认已经帮我们安装好了`Git`,所以我们就不需要自己去手动安装了,也没必要用源码去安装,太麻烦,如果没有安装`Git`,请使用如下命令进行安装:
```bash
[root@ansheng ~]# yum -y install git
```

安装NodeJS与npm

```bash
[root@ansheng ~]# yum -y install nodejs npm
[root@ansheng ~]# node -v
v0.10.42
[root@ansheng ansheng.me]# npm -v
1.3.6
```
使用`yum`安装的`nodejs`版本是`v0.10.42`,`npm`是`1.3.6`,也不知道这有什么关系


使用淘宝npm源

国内的这速度简直慢的不行,所以我决定用淘宝的`npm`源代替官方的,如果你的服务器是国外的就可以跳过这一步了.
```bash
[root@ansheng ~]# npm config set registry https://registry.npm.taobao.org
[root@ansheng ~]# npm info underscore # 如果上面配置正确这个命令会有字符串response
```

安装Hexo

这里是创建了`hexo`的管理目录
```bash
[root@ansheng ~]# cd /usr/local/
[root@ansheng local]# mkdir ansheng.me
[root@ansheng local]# cd ansheng.me/
[root@ansheng ansheng.me]# npm install -g hexo
```
初始化Hexo
```bash
[root@ansheng ansheng.me]# hexo init
```
安装依赖包
```bash
[root@ansheng ansheng.me]# npm install
```
生成hexo静态页面
```bash
[root@ansheng ansheng.me]# hexo g
INFO  Start processing
INFO  Files loaded in 419 ms
INFO  Generated: index.html
INFO  Generated: archives/index.html
INFO  Generated: archives/2016/index.html
INFO  Generated: archives/2016/07/index.html
INFO  Generated: fancybox/blank.gif
INFO  Generated: fancybox/fancybox_loading.gif
INFO  Generated: fancybox/fancybox_loading@2x.gif
INFO  Generated: fancybox/fancybox_overlay.png
INFO  Generated: fancybox/fancybox_sprite.png
INFO  Generated: fancybox/fancybox_sprite@2x.png
INFO  Generated: css/style.css
INFO  Generated: fancybox/jquery.fancybox.css
INFO  Generated: fancybox/jquery.fancybox.js
INFO  Generated: fancybox/jquery.fancybox.pack.js
INFO  Generated: js/script.js
INFO  Generated: fancybox/helpers/jquery.fancybox-buttons.css
INFO  Generated: fancybox/helpers/jquery.fancybox-buttons.js
INFO  Generated: fancybox/helpers/jquery.fancybox-media.js
INFO  Generated: fancybox/helpers/jquery.fancybox-thumbs.css
INFO  Generated: fancybox/helpers/jquery.fancybox-thumbs.js
INFO  Generated: css/fonts/FontAwesome.otf
INFO  Generated: css/fonts/fontawesome-webfont.eot
INFO  Generated: css/fonts/fontawesome-webfont.woff
INFO  Generated: fancybox/helpers/fancybox_buttons.png
INFO  Generated: css/fonts/fontawesome-webfont.ttf
INFO  Generated: css/fonts/fontawesome-webfont.svg
INFO  Generated: css/images/banner.jpg
INFO  Generated: 2016/07/17/hello-world/index.html
INFO  28 files generated in 1.42 s
```
启动Hexo
```bash
[root@ansheng ansheng.me]# hexo s -i 115.28.171.115 -p 80
INFO  Start processing
INFO  Hexo is running at http://115.28.171.115:80/. Press Ctrl+C to stop.
```
新开一个窗口用`curl`命令访问看看
```bash
[root@ansheng ~]# curl -I 115.28.171.115
HTTP/1.1 200 OK
X-Powered-By: Hexo
Content-Type: text/html
Date: Sat, 16 Jul 2016 16:48:08 GMT
Connection: keep-alive
```

## 配置Nginx

nginx主配置文件

```bash
[root@ansheng ansheng.me]# cd
[root@ansheng ~]# cd /etc/nginx/
[root@ansheng nginx]# rm -f nginx.conf
[root@ansheng nginx]# vim nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    use epoll;
    multi_accept on;
    worker_connections  1024;
}

http {
    server_tokens off;
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    charset utf-8;

    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;

    keepalive_timeout  65;

    gzip                        on;
    gzip_disable                "msie6";
    gzip_vary                   on;
    gzip_proxied                any;
    gzip_comp_level             6;
    gzip_buffers                16 8k;
    gzip_http_version           1.1;
    gzip_types                  text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    include /etc/nginx/conf.d/*.conf;
}
```
`www.ansheng.me`的配置文件
```bash
[root@ansheng nginx]# mkdir conf.d
[root@ansheng nginx]# cd conf.d/
[root@ansheng conf.d]# vim www.ansheng.me.conf
server {
    server_name                 ansheng.me www.ansheng.me;
    listen 			80;
    listen                      443 ssl http2;

    set $rewriterule https;

    if ($scheme = https) {
         set $rewriterule "${rewriterule}7";
         }

    if ($host ~* ^ansheng.me) {
         set $rewriterule "${rewriterule}8";
         }

    if ($rewriterule != "https78") {
         return 301 https://blog.ansheng.me$request_uri;
         break;
         }

    add_header                  Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";
    add_header                  X-Frame-Options "DENY";
    add_header                  X-Content-Type-Options "nosniff";
    add_header                  X-XSS-Protection "1; mode=block";
    add_header                  Set-Cookie "HttpOnly";
    add_header                  Set-Cookie "Secure";
    add_header                  Cache-Control "no-siteapp";
    add_header                  Public-Key-Pins 'pin-sha256="YLh1dUR9y6Kja30RrAn7JKnbQG/uEtLMkBgFF2Fuihg="; pin-sha256="kb6xLprt35abNnSn74my4Dkfya9arbk5zN5a60YzuqE="; max-age=2592000; includeSubDomains; report-uri="https://report-uri.io/report/fed937a11a268e8be35a12db8a648233"';

    #more_set_headers            "Server: ansheng-FWS/9.99";
    ssl_certificate             /etc/nginx/conf.d/...ansheng.me/ansheng.me_bundle.crt;
    ssl_certificate_key         /etc/nginx/conf.d/...ansheng.me/ansheng.me.key;
    ssl_trusted_certificate     /etc/nginx/conf.d/...ansheng.me/ansheng.me_bundle.crt;

    ssl_session_cache           shared:SSL:2m;
    ssl_session_timeout         12h;
    ssl_session_tickets         on;
    ssl_prefer_server_ciphers   on;
    ssl_protocols               TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers                 ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-CHACHA20-POLY1305:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!3DES:!MD5:!PSK;
    ssl_stapling                on;
    ssl_stapling_verify         on;
    resolver                    8.8.8.8 8.8.4.4 [2001:4860:4860::8888]:5353 [2001:4860:4860::8844]:5353;
    resolver_timeout            5s;

    error_page 404 /404.html;
    error_page 403 /403.html;
    error_page 500 502 503 504 /50x.html;
    access_log /var/log/nginx/logs/ansheng.me.access.log main;
    error_log /var/log/nginx/logs/ansheng.me.error.log warn;

    index index.html index.htm;
    root /usr/local/ansheng/public/;

    location  ~* .(sql|backup)$ {
         deny all;
        }

    location ~ ^/(.git|.svn|.bak|.backup|bak|backup)/ {
        deny all;
        }

    location ~ /.well-known/acme-challenge/(.*) {
        default_type text/plain;
        }

    location ~* \.(html|htm|xml|rss|atom|txt|xhtml)$ {
        expires 1d;
        }

    location ~* \.(css|gif|jpeg|jpg|js|png|ico|bmp|svg|doc|pdf|mp3|ogg|mp4|mpeg|webm|eot|ttf|woff)$ {
        expires 3d;
        }
}
```
创建nginx所需要的目录
```bash
[root@ansheng conf.d]# mkdir -p /var/log/nginx/logs/
[root@ansheng conf.d]# mkdir -p /var/cache/nginx/client_temp
```
测试以下看看语法是否通过
```bash
[root@ansheng conf.d]# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
启动`Nginx`
```bash
[root@ansheng conf.d]# nginx
[root@ansheng conf.d]# netstat -tlnp | grep "80"
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      13639/nginx: master
```

## 总结

1. 安装之前域名解析已经做好
2. 把域名的SSL证书放入对应的目录下
3. npm如果安装慢,你可以使用阿里的源进行安装
4. 安装好后记得打开页面测试下