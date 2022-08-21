# 编译安装LNMP（Linux+Nginx+MySQL+PHP

## 环境说明

LNMP即是Linux+Nginx+MySQL+PHP的简写，是一套动态的WEB程序。

软件版本说明：

|软件名|版本|
|:--|:--|
|Linux|CentOS6.7|
|Nginx|1.8.1|
|MySQL|5.5.49|
|PHP|5.3.27|

系统环境:

```bash
[root@ansheng ~]# uname -a
Linux ansheng 2.6.32-573.22.1.el6.x86_64 #1 SMP Wed Mar 23 03:35:39 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
[root@ansheng ~]# cat /etc/redhat-release 
CentOS release 6.7 (Final)
```

## 编译安装Nginx

创建管理Nginx服务的用户

```bash
[root@LNMP ~]# useradd nginx -s /sbin/nologin -M
```

下载Nginx软件包

```bash
[root@LNMP ~]# mkdir /application/tools -p
[root@LNMP ~]# cd /application/tools/
[root@LNMP tools]# wget http://nginx.org/download/nginx-1.8.1.tar.gz
```

安装依赖包

```bash
[root@LNMP tools]# yum -y install openssl openssl-devel pcre pcre-devel
```

编译安装Nginx

```bash
[root@LNMP tools]# tar xf nginx-1.8.1.tar.gz 
[root@LNMP tools]# cd nginx-1.8.1
[root@LNMP nginx-1.8.1]# ./configure --user=nginx --group=nginx --prefix=/application/nginx1.8.1/ --with-http_stub_status_module --with-http_ssl_module
[root@LNMP nginx-1.8.1]# make && make install
[root@LNMP nginx-1.8.1]# ln -s /application/nginx1.8.1/ /application/nginx
```

启动Nginx并设置开机启动

```bash
[root@LNMP ~]# /application/nginx/sbin/nginx -t
nginx: the configuration file /application/nginx1.8.1//conf/nginx.conf syntax is ok
nginx: configuration file /application/nginx1.8.1//conf/nginx.conf test is successful
[root@LNMP ~]# /application/nginx/sbin/nginx
[root@LNMP ~]# curl -I 127.0.0.1
HTTP/1.1 200 OK
Server: nginx/1.8.1
Date: Tue, 26 Apr 2016 07:11:06 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 26 Apr 2016 07:10:06 GMT
Connection: keep-alive
ETag: "571f144e-264"
Accept-Ranges: bytes
```

## 二进制包安装MySQL

创建管理MySQL服务的用户

```bash
[root@LNMP tools]# useradd mysql -s /sbin/nologin -M
```

下载5.5.49二进制包

```bash
[root@LNMP tools]# wget http://mirrors.sohu.com/mysql/MySQL-5.5/mysql-5.5.49-linux2.6-x86_64.tar.gz
```

二进制方式安装MySQL

```bash
[root@LNMP tools]# tar xf mysql-5.5.49-linux2.6-x86_64.tar.gz 
[root@LNMP tools]# mv mysql-5.5.49-linux2.6-x86_64 /application/mysql-5.5.49
[root@LNMP tools]# ln -s /application/mysql-5.5.49/ /application/mysql
[root@LNMP tools]# chown -R mysql.mysql /application/mysql/data/
[root@LNMP tools]# cd /application/mysql
[root@LNMP mysql]# ./scripts/mysql_install_db --user=mysql --basedir=/application/mysql/ --datadir=/application/mysql/data/
[root@LNMP mysql]# \cp support-files/my-small.cnf /etc/my.cnf
```

配置启动脚本

```bash
[root@LNMP mysql]# \cp support-files/mysql.server /etc/init.d/mysqld
 46 basedir=/application/mysql
 47 datadir=/application/mysql/data
```

启动MySQL并设置开机启动

```bash
[root@LNMP mysql]# /etc/init.d/mysqld start
Starting MySQL.. SUCCESS! 
[root@LNMP mysql]# chkconfig mysqld on
[root@LNMP mysql]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3834 mysql   10u  IPv4  22659      0t0  TCP *:mysql (LISTEN)
```

添加MySQL环境变量

```bash
[root@LNMP ~]# echo 'PATH=/application/mysql/bin:$PATH' >>/etc/profile
[root@LNMP ~]# source /etc/profile
[root@LNMP ~]# echo $PATH
/application/mysql/bin:/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
```

设置MySQL数据库的root密码

```bash
[root@LNMP ~]# mysqladmin -uroot password 'ansheng.me'   
```

登陆数据库删除不需要的用户及数据库

```bash
mysql> drop database test;
Query OK, 0 rows affected (0.01 sec)
mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| root | ::1       |
|      | lnmp      |
| root | lnmp      |
|      | localhost |
| root | localhost |
+------+-----------+
6 rows in set (0.00 sec)

mysql> drop user ''@'lnmp'; 
Query OK, 0 rows affected (0.00 sec)

mysql> drop user 'root'@'lnmp';
Query OK, 0 rows affected (0.00 sec)

mysql> drop user 'root'@'::1'; 
Query OK, 0 rows affected (0.00 sec)

mysql> drop user ''@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| root | localhost |
+------+-----------+
2 rows in set (0.00 sec)
``

## 编译安装PHP

安装PHP的依赖包

```bash
[root@LNMP ~]# yum -y install zlib libxml libjpeg freetype libpng gd  curl libiconv  zlib-devel libxml2-devel libjpeg-turbo-devel freetype-devel libpng-devel gd-devel curl-devel libxslt libxslt-devel libmcrypt mhash mcrypt libtool-ltdl-devel libmcrypt-devel
```

编译安装`libiconv`

```bash
[root@LNMP ~]# cd /application/tools/
[root@LNMP tools]# wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
[root@LNMP tools]# tar xf libiconv-1.14.tar.gz 
[root@LNMP tools]# cd libiconv-1.14
[root@LNMP libiconv-1.14]# ./configure --prefix=/usr/local/libiconv
[root@LNMP libiconv-1.14]# make && make install
```

下载PHP

官网：`http://cn.php.net`

```bash
[root@LNMP tools]# wget http://110.96.192.8:83/1Q2W3E4R5T6Y7U8I9O0P1Z2X3C4V5B/cn2.php.net/distributions/php-5.3.27.tar.gz
```

编译安装PHP

```bash
[root@LNMP tools]# tar xf php-5.3.27.tar.gz 
[root@LNMP tools]# cd php-5.3.27
./configure \
--prefix=/application/php5.3.27 \
--with-mysql=/application/mysql \
--with-iconv-dir=/usr/local/libiconv \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--disable-rpath \
--enable-safe-mode \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
--enable-inline-optimization \
--with-curl \
--with-curlwrappers \
--enable-mbregex \
--enable-fpm \
--enable-mbstring \
--with-mcrypt \
--with-gd \
--enable-gd-native-ttf \
--with-openssl \
--with-mhash \
--enable-pcntl \
--enable-sockets \
--with-xmlrpc \
--enable-zip \
--enable-soap \
--enable-short-tags \
--enable-zend-multibyte \
--enable-static \
--with-xsl \
--with-fpm-user=nginx \
--with-fpm-group=nginx \
--enable-ftp
[root@LNMP php-5.3.27]# make
[root@LNMP php-5.3.27]# make install
[root@LNMP php-5.3.27]# ln -s /application/php5.3.27/ /application/php
[root@LNMP php-5.3.27]# cp php.ini-production /application/php/lib/php.ini
[root@LNMP php-5.3.27]# cd /application/php/etc/
[root@LNMP etc]# cat php-fpm.conf | grep -vE ";|^$"
[global]
pid = /app/logs/php-fpm.pid
error_log = /app/logs/php-fpm.log
log_level = error
rlimit_files = 32768
events.mechanism = epoll
[www]
user = nginx
group = nginx
listen = 127.0.0.1:9000
listen.owner = nginx
listen.group = nginx
pm = dynamic
pm.max_children = 1024
pm.start_servers = 16
pm.min_spare_servers = 5
pm.max_spare_servers = 20
pm.max_requests = 2048
slowlog = /app/logs/$pool.log.slow
request_slowlog_timeout = 10
[root@LNMP etc]# mkdir /app/logs -p
```

make的时候出现以下问题的解决方法

```bash
/application/tools/php-5.3.27/sapi/cli/php: error while loading shared libraries: libmysqlclient.so.18: cannot open shared object file: No such file or directory
make: *** [ext/phar/phar.php] Error 127
```

解决方法：

```bash
[root@LNMP php-5.3.27]# mkdir ext/phar/phar.phar -p
```

make install出现的问题及解决方法

```bash
Installing PEAR environment:      /application/php5.3.27/lib/php/
/application/tools/php-5.3.27/sapi/cli/php: error while loading shared libraries: libmysqlclient.so.18: cannot open shared object file: No such file or directory
make[1]: *** [install-pear-installer] Error 127
make: *** [install-pear] Error 2
```

解决方法：

```bash
[root@LNMP php-5.3.27]# find / -name "libmysqlclient.so.18"
/application/mysql-5.5.49/lib/libmysqlclient.so.18
[root@LNMP php-5.3.27]# echo "/application/mysql-5.5.49/lib/" >> /etc/ld.so.conf
[root@LNMP php-5.3.27]# ldconfig
```

启动PHP并设置开机启动

```bash
[root@LNMP etc]# /application/php/sbin/php-fpm -t
[26-Apr-2016 16:24:18] NOTICE: configuration file /application/php5.3.27/etc/php-fpm.conf test is successful
[root@LNMP etc]# /application/php/sbin/php-fpm
[root@LNMP etc]# lsof -i :9000
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
php-fpm 4982  root    7u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4983 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4984 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4985 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4986 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4987 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4988 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4989 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4990 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4991 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4992 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4993 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4994 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4995 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4996 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4997 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
php-fpm 4998 nginx    0u  IPv4 118516      0t0  TCP localhost:cslistener (LISTEN)
[root@LNMP etc]# echo '/application/php/sbin/php-fpm' >> /etc/rc.local 
```

## PHP整合Nginx

```bash
[root@LNMP ~]# cd /application/nginx/conf/
[root@LNMP conf]# vim nginx.conf
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /application/nginx/html$fastcgi_script_name;
            include        fastcgi_params;
        }
    }
}
[root@LNMP conf]# /application/nginx/sbin/nginx -t
nginx: the configuration file /application/nginx1.8.1//conf/nginx.conf syntax is ok
nginx: configuration file /application/nginx1.8.1//conf/nginx.conf test is successful
[root@LNMP conf]# /application/nginx/sbin/nginx -s reload
```

### 创建Nginx连接PHP检测文件

```bash
[root@LNMP conf]# vim /application/nginx/html/phpinfo.php
<?php
phpinfo();
?>
```

### 创建

```bash
[root@LNMP conf]# vim /application/nginx/html/mysqlinfo.php
<?php
        $link_id=mysql_connect('localhost','root','ansheng.me') or mysql_error();

        if($link_id){
                echo "mysql successful !\n";
        }else{
                echo "mysql_error()";
        }
?>
```

### 打开浏览器测试

1、查看PHP

![lnmp-php](../images/2016/12/lnmp-php.png "lnmp-php")

2、查看MySQL

![lnmp-mysql](../images/2016/12/lnmp-mysql.png "lnmp-mysql")