# YUM全自动快速安装LAMP

环境查看

```bash
[root@AnSheng ~]# cat /etc/redhat-release
CentOS release 6.7 (Final)
[root@AnSheng ~]# uname -r
2.6.32-573.8.1.el6.x86_64
```

配置的是阿里的yum源和epel源，yum快速安装LAMP所需要的软件包

```bash
[root@AnSheng ~]# yum -y install gcc gcc-c++ autoconf httpd php mysql mysql-server php-mysql httpd-manual mod_ssl mod_perl mod_auth_mysql php-gd php-xml php-mbstring php-ldap php-pear php-xmlrpc php-bcmath mysql-connector-odbc mysql-devel libdbi-dbd-mysql net-snmp-devel curl-devel unixODBC-devel OpenIPMI-devel java-devel libssh2-devel openldap openldap-devel
```
启动apache并设置开机自启动
```bash
[root@AnSheng ~]# /etc/init.d/httpd start
Starting httpd: [ OK ]
[root@AnSheng ~]# chkconfig httpd on
```
启动MySQL设置密码并设置开机自启动
```bash
[root@AnSheng ~]# /etc/init.d/mysqld start
Initializing MySQL database: Installing MySQL system tables...
OK
Filling help tables...
OK
To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system
PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:
/usr/bin/mysqladmin -u root password 'new-password'
/usr/bin/mysqladmin -u root -h AnSheng password 'new-password'
Alternatively you can run:
/usr/bin/mysql_secure_installation
which will also give you the option of removing the test
databases and anonymous user created by default. This is
strongly recommended for production servers.
See the manual for more instructions.
You can start the MySQL daemon with:
cd /usr ; /usr/bin/mysqld_safe
You can test the MySQL daemon with mysql-test-run.pl
cd /usr/mysql-test ; perl mysql-test-run.pl
Please report any problems with the /usr/bin/mysqlbug script!
[ OK ]
Starting mysqld: [ OK ]
[root@AnSheng ~]# chkconfig mysqld on
[root@AnSheng ~]# mysqladmin -uroot password '123456'
```
验证apache是否与PHP结合
```bash
[root@AnSheng ~]# vim /var/www/html/index.php
?php
phpinfo();
?
```
在Linux下用curl命令查看是否输出PHP的信息
```bash
[root@AnSheng ~]# curl 127.0.0.1|grep 'PHP Version'
a href="http://www.php.net/"img border="0" src="/index.php?=PHPE9568F34-D428-11d2-A769-00AA001ACF42" alt="PHP Logo" //ah1 class="p"**<span style="color: #ff0000;">PHP Version 5.3.3</span>**/h1
 % Total % Received % Xferd Average Speed Time Time Time Current
 Dload Upload Total Spent Left Speed
100 16170 0 16170 0 0 3111k 0 --:--:-- --:--:-- --:--:-- 3111ktrtd class="e"PHP Version /tdtd class="v"5.3.3 /td/tr
100 59344 0 59344 0 0 10.5M 0 --:--:-- --:--:-- --:--:-- 41.1M
```
创建MySQL测试的PHP文件
```bash
[root@AnSheng ~]# vim /var/www/html/mysql.php
?php
 $link_id=mysql_connect('localhost','root','123456') or mysql_error();
 if($link_id){
 echo "mysql successful !\n";
 }else{
 echo "mysql_error()";
 }
?
```
浏览器打开访问mysql.php就可以查看MySQL与apache的结合情况了