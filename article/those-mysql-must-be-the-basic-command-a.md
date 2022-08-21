# 那些MySQL必会的基础命令（一）

## 启动与停止MySQL

### 单实例操作方法

**第一种启动方式**

```bash
[root@MySQL ~]# sed -i 's#/usr/local#/application#g' /application/mysql/bin/mysqld_safe
[root@MySQL ~]# /application/mysql/bin/mysqld_safe &
[1] 2869
[root@MySQL ~]# 150317 22:09:19 mysqld_safe Logging to '/application/mysql/data/MySQL.err'.
150317 22:09:19 mysqld_safe Starting mysqld daemon with databases from /application/mysql/data
[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3085 mysql   10u  IPv4  25607      0t0  TCP *:mysql (LISTEN)
```

**第二种启动方式**

```bash
[root@MySQL ~]# cp /application/mysql/support-files/mysql.server /etc/init.d/mysqld
[root@MySQL ~]# vim /etc/init.d/mysqld 
[root@MySQL ~]# sed -n 46,47p /etc/init.d/mysqld 
basedir=/application/mysql/
datadir=/application/mysql/data/
[root@MySQL ~]# chmod +x /etc/init.d/mysqld 
[root@MySQL ~]# /etc/init.d/mysqld start
Starting MySQL.. SUCCESS! 
[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3376 mysql   10u  IPv4  26230      0t0  TCP *:mysql (LISTEN)
[root@MySQL ~]# chkconfig mysqld on
```

### 多实例启动方式

**第一种启动方式**

```bash
[root@MySQL ~]# mysqld_safe --defaults-file=/data/3306/my.cnf &
[1] 7010
[root@MySQL ~]# 150317 22:22:46 mysqld_safe Logging to '/data/3306/mysql_oldboy3306.err'.
150317 22:22:46 mysqld_safe Starting mysqld daemon with databases from /data/3306/data

[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  7732 mysql   12u  IPv4  16735      0t0  TCP *:mysql (LISTEN)
```

**第二种启动方式**

```bash
[root@MySQL ~]# chmod +x /data/3306/mysql 
[root@MySQL ~]# /data/3306/mysql start
Starting MySQL...
[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  6184 mysql   12u  IPv4  15526      0t0  TCP *:mysql (LISTEN)
```

### MySQL关闭方式

**第一种关闭方式**

```bash
[root@MySQL ~]# /application/mysql/bin/mysqld_safe &
[1] 4417
[root@MySQL ~]# 150317 22:30:48 mysqld_safe Logging to '/application/mysql/data/MySQL.err'.
150317 22:30:48 mysqld_safe Starting mysqld daemon with databases from /application/mysql/data

[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  4633 mysql   10u  IPv4  27506      0t0  TCP *:mysql (LISTEN)
[root@MySQL ~]# mysqladmin -uroot -p123456 shutdown
150317 22:30:55 mysqld_safe mysqld from pid file /application/mysql/data/MySQL.pid ended
[1]+  Done                    /application/mysql/bin/mysqld_safe
[root@MySQL ~]# lsof -i :3306
```

**第二种停止方式**

```bash
[root@MySQL ~]# /etc/init.d/mysqld start
Starting MySQL.. SUCCESS! 
[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3664 mysql   10u  IPv4  26620      0t0  TCP *:mysql (LISTEN)
[root@MySQL ~]# /etc/init.d/mysqld stop
Shutting down MySQL. SUCCESS! 
[root@MySQL ~]# lsof -i :3306
```

- 第三种停止方式

```bash
[root@MySQL ~]# lsof -i :3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  3376 mysql   10u  IPv4  26230      0t0  TCP *:mysql (LISTEN)
[root@MySQL ~]# pkill mysqld
[root@MySQL ~]# lsof -i :3306
```

- 多实例关闭方式

```bash
[root@MySQL ~]# mysqld_safe --defaults-file=/data/3306/my.cnf &
[1] 7777
[root@MySQL ~]# 150317 22:38:14 mysqld_safe Logging to '/data/3306/mysql_oldboy3306.err'.
150317 22:38:14 mysqld_safe Starting mysqld daemon with databases from /data/3306/data

[root@MySQL ~]# mysqladmin -uroot -p3306 -S /data/3306/mysql.sock shutdown
150317 22:38:17 mysqld_safe mysqld from pid file /data/3306/mysqld.pid ended

[1]+  Done                    mysqld_safe --defaults-file=/data/3306/my.cnf
[root@MySQL ~]# 
[root@MySQL ~]# lsof -i :3306
```

## MySQL登录方法

### 单实例登录

**语法格式：**
	mysql –u[user] –p[password]

```bash
[root@MySQL ~]# mysql -uroot -p123456
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.5.32 MySQL Community Server (GPL)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```bash

### 单实例远程登录

**语法格式：**
	mysql –u[user] –p[password] –h[hostname/ip]

```bash
[root@MySQL-Client ~]# mysql -uroot -p123456 -h10.10.10.100
[root@MySQL-Client ~]# mysql -uroot -p123456 -hmysql --也可以指定hostname方式链接
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 6
Server version: 5.5.32 MySQL Community Server (GPL)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

### 多实例登录

**语法格式：**
	mysql –u[user] –p[passwod] –S [sock path]

```bash
[root@MySQL ~]# mysql -uroot -p3306 -S /data/3306/mysql.sock 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.5.32-log Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

### 远程登录多实例

**语法格式：**
	mysql –u[user] –p[passwod] –S [sock path] –P[port]

```bash
[root@MySQL ~]# mysql -uroot -p123456 -h10.10.10.10 -P3306
[root@MySQL ~]# mysql -uroot -p123456 -hMySQL -P3306 --也可以指定hostname方式链接
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 5.5.32-log Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## 设置及更改MySQ root密码

### 没有密码设置密码

**语法格式：**
	mysqladmin –u[user] password ‘[password]’

```bash
[root@MySQL ~]# /etc/init.d/mysqld start
Starting MySQL.. SUCCESS! 
[root@MySQL ~]# mysql -uroot -p123456
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
[root@MySQL ~]# mysqladmin -uroot password '123456'
[root@MySQL ~]# mysql -uroot -p123456
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.5.32 MySQL Community Server (GPL)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

### 多实例没有密码设置密码

**语法格式：**
	mysqladmin –u[user] password ‘[password]’ –S [sock path]

```bash
[root@MySQL ~]# /data/3307/mysql  start
Starting MySQL...
[root@MySQL ~]# mysql -uroot -p123456 -S /data/3307/mysql.sock 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
[root@MySQL ~]# mysqladmin -uroot password '123456' -S /data/3307/mysql.sock 
[root@MySQL ~]# mysql -uroot -p123456 -S /data/3307/mysql.sock 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.5.32 Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

### 有密码更改密码

**语法格式：**
	mysqladmin –u[user] –p’[password]’password ‘[password]’

```bash
[root@MySQL ~]# mysqladmin -uroot -p'123456' password linux
```

### 多实例有密码更改密码

**语法格式：**
	mysqladmin –u[user] –p’[password]’password ‘[password]’ –S [sock path]

```bash
[root@MySQL ~]# mysqladmin -uroot -p'3306' password ‘123456’ -S /data/3306/mysql.sock
```

### SQL语句更改密码方式

```bash
mysql> update mysql.user set password=password("123456") where user='root';
mysql> flush privileges;
```

### 单实例密码丢失更改密码

```bash
[root@MySQL ~]# /etc/init.d/mysqld stop
[root@MySQL ~]# mysqld_safe --skip-grant-tables --user=mysql &
[root@MySQL ~]# mysql
mysql> update mysql.user set password=password('123456') where user = 'root' and host = 'localhost';
mysql> flush privileges;
mysql> quit
[root@MySQL ~]# mysqladmin -uroot -p123456 shutdown
[root@MySQL ~]# /etc/init.d/mysqld start
[root@MySQL ~]# mysql -uroot -p123456
```

### 多实例密码丢失更改密码

```bash
[root@MySQL ~]# killall mysqld
[root@MySQL ~]# mysqld_safe --defaults-file=/data/3310/my.cnf --skip-grant-table &
[root@MySQL ~]# mysql -uroot -p -S /data/3310/mysql.sock
mysql> update mysql.user set password=password('123456') where user = 'root' and host = 'localhost';
mysql> flush privileges;
mysql> quit
[root@MySQL ~]# /data/3310/mysql restart
[root@MySQL ~]# mysql -uroot -p123456 -S /data/3310/mysql.sock
```

## 数据库的备份与还原

```bash
[root@MySQL ~]# mysqldump -uroot -p123456 -S /data/3307/mysql.sock -B db >/root/msyql.bak
```

-B和不加-B

```bash
[root@MySQL ~]# grep -vE "#|\/|^$|--" bak_b.sql 
USE `oldboy`;
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `id` int(4) NOT NULL AUTO_INCREMENT,
  `name` char(20) NOT NULL,
  `age` tinyint(2) NOT NULL DEFAULT '0',
  `dept` varchar(16) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `index_dept` (`dept`),
  KEY `index_name` (`name`(8))
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
LOCK TABLES `student` WRITE;
UNLOCK TABLES;
DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
  `id` int(4) NOT NULL AUTO_INCREMENT,
  `name` char(20) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
LOCK TABLES `test` WRITE;
INSERT INTO `test` VALUES (1,'oldboy'),(2,'oldgirl'),(3,'inca'),(4,'zuma'),(5,'kaka');
UNLOCK TABLES;
```

**逻辑备份：**
把数据库里的数据以SQL语句的方式导出来，然后还原的时候相当于执行了导出来的SQL的语句

恢复数据库

```bash
[root@mysql ~]# mysql -uroot -poldboy -S /data/3310/mysql.sock < bak_b.sql
```

## 创建与显示数据库

### 创建数据库

**语法格式：**
	create dtabase [db_name];

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)

mysql> create database db;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db                 |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)
```

- 查看db库的字符集,默认你数据库是什么字符集，创建出来的库就是什么字符集，当然字符集也可以指定。

```bash
mysql> show create database db;
+----------+---------------------------------------------------------------+
| Database | Create Database                                               |
+----------+---------------------------------------------------------------+
| db       | CREATE DATABASE `db` /*!40100 DEFAULT CHARACTER SET latin1 */ |
+----------+---------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 创建指定字符集的数据库

- 创建GBK字符集数据库：

```bash
create database oldboy_gbk DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci; 
```

- 创建UTF8数据库：

```bash
create database oldboy_utf8 CHARACTER SET utf8  COLLATE utf8_general_ci;
```

- 创建一个名为db_gbk的GBK字符集数据库

```bash
mysql> create database db_gbk default character set gbk collate gbk_chinese_ci;
Query OK, 1 row affected (0.00 sec)
```

- 查看一个库是什么字符集

**语法格式：**
	show create database [db_name];

```bash
mysql> show create database db_gbk;
+----------+----------------------------------------------------------------+
| Database | Create Database                                                |
+----------+----------------------------------------------------------------+
| db_gbk   | CREATE DATABASE `db_gbk` /*!40100 DEFAULT CHARACTER SET gbk */ |
+----------+----------------------------------------------------------------+
1 row in set (0.00 sec)
```

- 查看可以设定的字符集

```bash
mysql> show character set;
+----------+-----------------------------+---------------------+--------+
| Charset  | Description                 | Default collation   | Maxlen |
+----------+-----------------------------+---------------------+--------+
| big5     | Big5 Traditional Chinese    | big5_chinese_ci     |      2 |
| dec8     | DEC West European           | dec8_swedish_ci     |      1 |
| cp850    | DOS West European           | cp850_general_ci    |      1 |
| hp8      | HP West European            | hp8_english_ci      |      1 |
| koi8r    | KOI8-R Relcom Russian       | koi8r_general_ci    |      1 |
| latin1   | cp1252 West European        | latin1_swedish_ci   |      1 |
| latin2   | ISO 8859-2 Central European | latin2_general_ci   |      1 |
| swe7     | 7bit Swedish                | swe7_swedish_ci     |      1 |
| ascii    | US ASCII                    | ascii_general_ci    |      1 |
| ujis     | EUC-JP Japanese             | ujis_japanese_ci    |      3 |
| sjis     | Shift-JIS Japanese          | sjis_japanese_ci    |      2 |
| hebrew   | ISO 8859-8 Hebrew           | hebrew_general_ci   |      1 |
| tis620   | TIS620 Thai                 | tis620_thai_ci      |      1 |
| euckr    | EUC-KR Korean               | euckr_korean_ci     |      2 |
| koi8u    | KOI8-U Ukrainian            | koi8u_general_ci    |      1 |
| gb2312   | GB2312 Simplified Chinese   | gb2312_chinese_ci   |      2 |
| greek    | ISO 8859-7 Greek            | greek_general_ci    |      1 |
| cp1250   | Windows Central European    | cp1250_general_ci   |      1 |
| gbk      | GBK Simplified Chinese      | gbk_chinese_ci      |      2 |
| latin5   | ISO 8859-9 Turkish          | latin5_turkish_ci   |      1 |
| armscii8 | ARMSCII-8 Armenian          | armscii8_general_ci |      1 |
| utf8     | UTF-8 Unicode               | utf8_general_ci     |      3 |
| ucs2     | UCS-2 Unicode               | ucs2_general_ci     |      2 |
| cp866    | DOS Russian                 | cp866_general_ci    |      1 |
| keybcs2  | DOS Kamenicky Czech-Slovak  | keybcs2_general_ci  |      1 |
| macce    | Mac Central European        | macce_general_ci    |      1 |
| macroman | Mac West European           | macroman_general_ci |      1 |
| cp852    | DOS Central European        | cp852_general_ci    |      1 |
| latin7   | ISO 8859-13 Baltic          | latin7_general_ci   |      1 |
| utf8mb4  | UTF-8 Unicode               | utf8mb4_general_ci  |      4 |
| cp1251   | Windows Cyrillic            | cp1251_general_ci   |      1 |
| utf16    | UTF-16 Unicode              | utf16_general_ci    |      4 |
| cp1256   | Windows Arabic              | cp1256_general_ci   |      1 |
| cp1257   | Windows Baltic              | cp1257_general_ci   |      1 |
| utf32    | UTF-32 Unicode              | utf32_general_ci    |      4 |
| binary   | Binary pseudo charset       | binary              |      1 |
| geostd8  | GEOSTD8 Georgian            | geostd8_general_ci  |      1 |
| cp932    | SJIS for Windows Japanese   | cp932_japanese_ci   |      2 |
| eucjpms  | UJIS for Windows Japanese   | eucjpms_japanese_ci |      3 |
+----------+-----------------------------+---------------------+--------+
39 rows in set (0.00 sec)
```

### 显示数据库

**语法格式**
	show databases [db_name]

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db                 |
| db_gbk             |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)
```

- 查看指定数据库

```bash
mysql> show databases like 'db';
+---------------+
| Database (db) |
+---------------+
| db            |
+---------------+
1 row in set (0.00 sec)
```

- 匹配查找

```bash
mysql> show databases like 'db%';
+----------------+
| Database (db%) |
+----------------+
| db             |
| db_gbk         |
+----------------+
2 rows in set (0.00 sec)
```

### 显示数据库中的表

**语法格式**
	show tables [db_name]

- 查看指定库存在的表

```bash
mysql> show tables from db;
Empty set (0.00 sec)
```

- 查看当前库存在的表

```
mysql> use db
Database changed
mysql> show tables;
Empty set (0.00 sec)
```

### 查看其他相关

- 显示当前所在数据库

```bash
mysql> select database();
+------------+
| database() |
+------------+
| NULL       |
+------------+
1 row in set (0.00 sec)

mysql> use db
Database changed
mysql> select database();
+------------+
| database() |
+------------+
| db         |
+------------+
1 row in set (0.00 sec)
```

- 查看当前数据库版本

```bash
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.5.32    |
+-----------+
1 row in set (0.00 sec)
```

- 查看当前的用户

```bash
mysql> select user();
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)
```


- 企业里怎么创建数据库呢？

1. 根据开发的程序确定字符集（建议UTF8）
2. 编译时候指定字符集，例如：

```bash
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DEXTRA_CHARSETS=gbk,gb2312,utf8,ascii \
```

3. 编译的时候没有指定字符集或者制定了和程序不同的字符集，如何解决？

指定字符集创建数据库即可。

```bash
create database oldboy_gbk DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;    #创建GBK字符集数据库：
create database oldboy_utf8 CHARACTER SET utf8  COLLATE utf8_general_ci;        #创建UTF8数据库
```

## 删除库及表

### 删除库

**语法格式**
	drop databases [db_name]

```bash
mysql> show databases like 'db';
+---------------+
| Database (db) |
+---------------+
| db            |
+---------------+
1 row in set (0.00 sec)

mysql> drop database db;
Query OK, 0 rows affected (0.00 sec)

mysql> show databases like 'db';
Empty set (0.00 sec)
```

## 创建与删除用户

### 创建用户

**语法格式**
	create user ‘[user]’@’[hostname]’ IDENTIFIED BY '[password]';

```bash
mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| root | localhost |
+------+-----------+
2 rows in set (0.00 sec)

mysql> create user 'db'@'localhost' identified by '123456';
Query OK, 0 rows affected (0.03 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| db   | localhost |
| root | localhost |
+------+-----------+
3 rows in set (0.00 sec)
```

### 删除用户

**语法格式**
	drop user ‘[user]’@’[hostame]’

```bash
mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| db   | localhost |
| root | localhost |
+------+-----------+
3 rows in set (0.00 sec)

mysql> drop user 'db'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| root | localhost |
+------+-----------+
2 rows in set (0.00 sec)
```

### SQL语句删除用户

如drop删除不了（一般是特殊字符或大写），可以用下面的方式删除

**语法格式：**
	delete from mysql.usr where user = ‘[user]’ and host = ‘[hostname]’

```bash
mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | MySQL     |
| root | localhost |
+------+-----------+
4 rows in set (0.00 sec)

mysql> drop user 'root'@'MySQL';
Query OK, 0 rows affected (0.00 sec)

mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | MySQL     |
| root | localhost |
+------+-----------+
4 rows in set (0.00 sec)

mysql> delete from mysql.user where user = 'root' and host = 'MySQL';
Query OK, 1 row affected (0.00 sec)

mysql> select user,host from mysql.user;
+------+-----------+
| user | host      |
+------+-----------+
| root | localhost |
+------+-----------+
3 rows in set (0.00 sec)
```

## 数据库授权

### 查看用户被赋予的权限

**语法格式**
	show grats for ‘[user]’@’[hostname]’;

```bash
mysql> show grants for 'db'@'localhost';
+-----------------------------------------------------------------------------------------------------------+
| Grants for db@localhost                                                                                   |
+-----------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'db'@'localhost' IDENTIFIED BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' |
| GRANT ALL PRIVILEGES ON `db`.* TO 'db'@'localhost' WITH GRANT OPTION                                      |
+-----------------------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)
```

- 以不同的方式显示

```bash
mysql> show grants for 'db'@'localhost'\G;
*************************** 1. row ***************************
Grants for db@localhost: GRANT USAGE ON *.* TO 'db'@'localhost' IDENTIFIED BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9'
*************************** 2. row ***************************
Grants for db@localhost: GRANT ALL PRIVILEGES ON `db`.* TO 'db'@'localhost' WITH GRANT OPTION
2 rows in set (0.00 sec)
```

### 添加用户授权

|grant|all|on dbname.*|to username@localhost|identified by 'passwd'|
|:--|:--|:--|:--|:--|
|授权命令|对应权限|目标：库和表|用户名和客户端主机|用户密码|

**语法格式**
 GRANT ALL ON db1.* TO 'jeffrey'@'localhost';

```bash
mysql> show grants for 'db'@'localhost';
+-----------------------------------------------------------------------------------------------------------+
| Grants for db@localhost                                                                                   |
+-----------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'db'@'localhost' IDENTIFIED BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' |
+-----------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
mysql> grant select on db.* to 'db'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> show grants for 'db'@'localhost';
+-----------------------------------------------------------------------------------------------------------+
| Grants for db@localhost                                                                                   |
+-----------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'db'@'localhost' IDENTIFIED BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' |
| GRANT SELECT ON `db`.* TO 'db'@'localhost'                                                                |
+-----------------------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)
```

### 移除用户权限

**语法格式**
	revoke select on [db_name].[table_name] from '[user]'@'[hostname]';

```bash
mysql> show grants for 'db'@'localhost';
+-----------------------------------------------------------------------------------------------------------+
| Grants for db@localhost                                                                                   |
+-----------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'db'@'localhost' IDENTIFIED BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' |
| GRANT SELECT ON `db`.* TO 'db'@'localhost'                                                                |
+-----------------------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> revoke select on db.* from 'db'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> show grants for 'db'@'localhost';
+-----------------------------------------------------------------------------------------------------------+
| Grants for db@localhost                                                                                   |
+-----------------------------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'db'@'localhost' IDENTIFIED BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' |
+-----------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 创建一个和root等价的管理员授权方法

```bash
grant all privileges on *.* to system@'localhost' identified by 'oldboy123' with grant option;
flush privileges;
```

### 企业生产环境是如何授权用户权限？

- 博客，CMS等产品如何授权？

对于WEB连接也难怪乎授权尽量采用最小化原则，很多开源软件都是WEB界面安装，在安装期间除了select,insert,update,delete4个权限外，还需要create,drop等比较危险的权限

```bash
mysql> grant select,insert,update,delete,create,drop on blog.* to 'blog'@'localhost' identified by '123456';
```

常规情况下授权select,insert,update,delete4个权限即可，有的开源软件，例如：discuz还需要create,drop的等比较危险的权限

- 生成数据库表后要收回create,drop授权

```bash
mysql> revoke create,drop on blog.* from 'blog'@'localhost';
```

- 生产环境针对主库（写为主读为辅）用户的授权：

普通环境：
本机：LNMP,LAMP环境护具库授权

```bash
mysql> grant all privileges on blog.* to ‘blog’@’localhost’ identified by ‘123456’;
```

应用服务器和数据库服务器不在一个主机上的授权

```bash
mysql> grant all privileges on blog.* to ‘blog’@’10.0.0.%’ identified by ‘123456’;
```

严格的授权：重视安全，忽略了方便

```bash
mysql> grant select on blog.* to ‘blog’@’10.0.0.%’ identified by ‘123465’;
```

- 生产环境从库（只读）用户的授权

```bash
mysql> grant select on blog.* to ‘blog’@’10.0.0.%’ identified by ‘123456’;
```

说明：这里表示给10.0.0.0/24的用户blog管理blog数据库的所有表（*表示所有表）只读权限（select）,密码为123456

## 表的相关操作

### 创建表

<pre>
建表的基本命令语法：
create table <表名> (
 <字段名1> <类型1> ,
…
<字段名n> <类型n>);
</pre>

```bash
CREATE TABLE `test` (
  `id` int(4) NOT NULL,
  `name` char(20) NOT NULL,
  `age` tinyint(2) NOT NULL DEFAULT '0',
  `dept` varchar(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 #创建时指定字符集
```

索引类似文件系统的inode,记录类似文件系统的block,
索引要创建再where条件列上，而非要查询的内容列，才能加快查询速度

主键索引：类似身份证 唯一标示表内的一条记录

```bash
create table test(
id int(4) not null AUTO_INCREMENT,
name char(20) not null,
age tinyint(2)  NOT NULL default '0',
dept varchar(16)  default NULL,
primary key(id), #主键索引，记录唯一
KEY index_name (name) #普通索引，记录可能不唯一
);
```

### 删除表

**语法格式**
	drop tables [table_name]

```bash
mysql> show tables from db;
+--------------+
| Tables_in_db |
+--------------+
| test         |
+--------------+
1 row in set (0.00 sec)

mysql> drop table test;
Query OK, 0 rows affected (0.01 sec)

mysql> show tables from db;
Empty set (0.00 sec)
```

### 查看表的内容

**命令语法：**
	select <字段1，字段2…> from <表名> where <表达式>

其中select fom where是不能随便改的


```bash
mysql> select *  from oldboy.test;
```

- 条件查询

```bash
mysql> select id,name from test whee name="ansheng";
```

- 多个条件查询

```bash
mysql> select id,name from test where name='ansheng' or id=5;
```

- 排序

```bash
mysql> select id,name from test order by id asc;
+----+---------+
| id | name    |
+----+---------+
|  1 | oldboy  |
|  2 | oldgirl |
|  3 | inca    |
|  4 | zuma    |
|  5 | kaka    |
+----+---------+
5 rows in set (0.00 sec)

mysql> select id,name from test order by id desc;
+----+---------+
| id | name    |
+----+---------+
|  5 | kaka    |
|  4 | zuma    |
|  3 | inca    |
|  2 | oldgirl |
|  1 | oldboy  |
+----+---------+
5 rows in set (0.00 sec)

mysql> select id,name from test where id >2 order by id desc; 
+----+------+
| id | name |
+----+------+
|  5 | kaka |
|  4 | zuma |
|  3 | inca |
+----+------+
3 rows in set (0.00 sec)
```

### 多表查询

```bash
mysql> select student.Sno,student.Sname,course.Cname,SC.Grade from student,course,SC where student.Sno=SC.Sno and course.Cno=SC.Cno order by student.Sname;
```
### 查看表建立结构

```bash
mysql> desc student;
mysql> show columns from student;
```

查看建立的表语句（可以看索引及创建表的相关信息）

```bash
mysql> show create table db.test\G
*************************** 1. row ***************************
       Table: test
Create Table: CREATE TABLE `test` (
  `id` int(4) NOT NULL,
  `name` char(20) NOT NULL,
  `age` tinyint(2) NOT NULL DEFAULT '0',
  `dept` varchar(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

### 更改表名称

**语法格式**
	rename table tb_name to new_tb_name

```bash
mysql> use db
Database changed
mysql> show tables;
+--------------+
| Tables_in_db |
+--------------+
| test         |
+--------------+
1 row in set (0.00 sec)

mysql> rename table test to linux;
Query OK, 0 rows affected (0.08 sec)
mysql> show tables;
+--------------+
| Tables_in_db |
+--------------+
| linux        |
+--------------+
1 row in set (0.00 sec)
```

- 第二种改名方法

```bash
mysql> alter table kkk rename to test;
```

### 往表里插入数据

按规矩指定所有列名，并且每列都插入值

**语法格式**
	insert into tb_name(col_name) values(‘character’);

```bash
mysql> insert into test(id,name) values(1,'natasha');
Query OK, 1 row affected (0.00 sec)

mysql> select * from test;
+----+---------+-----+------+
| id | name    | age | dept |
+----+---------+-----+------+
|  1 | natasha |   0 | NULL |
+----+---------+-----+------+
1 row in set (0.00 sec)
```

由于ID列为自增的，所以，可以只在name列插入值

```bash
mysql> insert into test(name) values('harry');
Query OK, 1 row affected, 1 warning (0.00 sec)

mysql> select * from test;
+----+---------+-----+------+
| id | name    | age | dept |
+----+---------+-----+------+
|  1 | natasha |   0 | NULL |
|  0 | harry   |   0 | NULL |
+----+---------+-----+------+
2 rows in set (0.00 sec)
```

如果不指定列，就要按规矩为每列都插入恰当的值

```bash
insert into test values(3,’inca’)
```

批量插入数据方法

```bash
insert into test values(4,'zuma'),(5,'kaka');
insert into `test` VALUES (1,'oldboy'),(2,'oldgirl'),(3,'inca'),(4,'zuma'),(5,'kaka');
```

### 删除表中的数据

- 清空表内容

```bash
mysql> truncate table test;
mysql> delete from test;
```

- 删除字段：  

```bash
mysql> ALTER TABLE table_name DROP field_name; 
```