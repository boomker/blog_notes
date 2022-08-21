# MySQL5.5字符集介绍

## 什么是字符集？

字符集，character set，就是一套表示字符的符号和这些的符号的底层编码；而校验规则，则是在字符集内用于比较字符的一套规则。

简单的说，字符集就是一套文字符号及其编码、比较规则的集合，第一个计算机字符集ASC2，MySQL数据库字符集包括字符集和校对规则两个概念，字符集是定义数据库里面的内容字符串的存储方式，而校对规则是定义比较字符串的方式

编译MySQL的时候，指定字符集了，这样以后建库的时候就直接create database db_name;

二进制安装MySQL，就没有指定字符集了，此时字符集默认就是latinl，此时需要建立UTF8字符集的库，就要指定UTF8字符集建库 

```bash
create database oldboy default character set utf8 deaultcollate=utf8_general_ci;
```

### 在互联网环境中，使用MySQL时常用的字符集

|常用字符集|一个汉字长度（字节）|说明|
|:--|:--|:--|
|GBK|2|不是国际标准，对中文支持的很好|
|UTF-8|3|中英文混合的环境，建议使用此字符集，用的比较多的|
|Latinl|1|MySQL的默认字符集|
|Utf8mb4|4|UTF-8 Unicode,移动互联网|

### 工作中MySQL如何选择字符集？

1. 如果处理各种各样的文字，发布到不同语言国家地区，应选Unicode字符集。对mysql来说就是UTF-8（每个汉字三字节），如果应用需处理英文，仅有少量汉字UTF-8更好。
2. 如只需支持中文，并且数据量很大，性能要求也很高，可以选择GBK（定长 每个汉字占双字节，英文也占双字节），如果需大量运算，比较排序等，定义长字符集，更快，性能高
3. 处理移动互联网业务，可能需要使用utf8mb4字符集

## MySQL字符集相关命令

### 查看MySQL字符集设置情况

```bash
mysql> show variables like 'character%';
[root@MySQL ~]# mysql -uroot -p3306 -S /data/3306/mysql.sock -e "show variables like 'character%'"
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+  
| character_set_client     | utf8                       |  --客户端字符集
| character_set_connection | utf8                       |	 --连接字符集，客户端
| character_set_database   | utf8                       |  --数据库字符集，配置文件指定或建库表指定
| character_set_filesystem | binary                     |  --文件系统的字符集
| character_set_results    | utf8                       |  --返回结果字符集 客户端
| character_set_server     | utf8                       |  --服务器字符集，配置文件指定或建库表指定
| character_set_system     | utf8                       |  --系统的字符集
```

### 查看字符集及校验规则

```bash
[root@MySQL ~]# mysql -uroot -p3306 -S /data/3306/mysql.sock -e "show character set;"
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
```

### 查看库的字符集

```bsh
mysql> show create database db;
+----------+-------------------------------------------------------------+
| Database | Create Database                                             |
+----------+-------------------------------------------------------------+
| db       | CREATE DATABASE `db` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+-------------------------------------------------------------+
```

### 查看表的字符集

```bash
mysql> show create table db_tb\G
*************************** 1. row ***************************
       Table: db_tb
Create Table: CREATE TABLE `db_tb` (
  `id` int(4) DEFAULT NULL,
  `name` varchar(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

### 查询MySQL数据库所支持的字符集和校验规则

- 查询所有

```bash
mysql> show collation;
```

- like进行筛选，之查看utf8的

```bash
mysql> show collation like 'utf8%';
+--------------------------+---------+-----+---------+----------+---------+
| Collation                | Charset | Id  | Default | Compiled | Sortlen |
+--------------------------+---------+-----+---------+----------+---------+
| utf8_general_ci          | utf8    |  33 | Yes     | Yes      |       1 |
| utf8_bin                 | utf8    |  83 |         | Yes      |       1 |
| utf8_unicode_ci          | utf8    | 192 |         | Yes      |       8 |
| utf8_icelandic_ci        | utf8    | 193 |         | Yes      |       8 |
| utf8_latvian_ci          | utf8    | 194 |         | Yes      |       8 |
| utf8_romanian_ci         | utf8    | 195 |         | Yes      |       8 |
| utf8_slovenian_ci        | utf8    | 196 |         | Yes      |       8 |
| utf8_polish_ci           | utf8    | 197 |         | Yes      |       8 |
| utf8_estonian_ci         | utf8    | 198 |         | Yes      |       8 |
| utf8_spanish_ci          | utf8    | 199 |         | Yes      |       8 |
| utf8_swedish_ci          | utf8    | 200 |         | Yes      |       8 |
| utf8_turkish_ci          | utf8    | 201 |         | Yes      |       8 |
| utf8_czech_ci            | utf8    | 202 |         | Yes      |       8 |
| utf8_danish_ci           | utf8    | 203 |         | Yes      |       8 |
| utf8_lithuanian_ci       | utf8    | 204 |         | Yes      |       8 |
| utf8_slovak_ci           | utf8    | 205 |         | Yes      |       8 |
| utf8_spanish2_ci         | utf8    | 206 |         | Yes      |       8 |
| utf8_roman_ci            | utf8    | 207 |         | Yes      |       8 |
| utf8_persian_ci          | utf8    | 208 |         | Yes      |       8 |
| utf8_esperanto_ci        | utf8    | 209 |         | Yes      |       8 |
| utf8_hungarian_ci        | utf8    | 210 |         | Yes      |       8 |
| utf8_sinhala_ci          | utf8    | 211 |         | Yes      |       8 |
| utf8_general_mysql500_ci | utf8    | 223 |         | Yes      |       1 |
| utf8mb4_general_ci       | utf8mb4 |  45 | Yes     | Yes      |       1 |
| utf8mb4_bin              | utf8mb4 |  46 |         | Yes      |       1 |
| utf8mb4_unicode_ci       | utf8mb4 | 224 |         | Yes      |       8 |
| utf8mb4_icelandic_ci     | utf8mb4 | 225 |         | Yes      |       8 |
| utf8mb4_latvian_ci       | utf8mb4 | 226 |         | Yes      |       8 |
| utf8mb4_romanian_ci      | utf8mb4 | 227 |         | Yes      |       8 |
| utf8mb4_slovenian_ci     | utf8mb4 | 228 |         | Yes      |       8 |
| utf8mb4_polish_ci        | utf8mb4 | 229 |         | Yes      |       8 |
| utf8mb4_estonian_ci      | utf8mb4 | 230 |         | Yes      |       8 |
| utf8mb4_spanish_ci       | utf8mb4 | 231 |         | Yes      |       8 |
| utf8mb4_swedish_ci       | utf8mb4 | 232 |         | Yes      |       8 |
| utf8mb4_turkish_ci       | utf8mb4 | 233 |         | Yes      |       8 |
| utf8mb4_czech_ci         | utf8mb4 | 234 |         | Yes      |       8 |
| utf8mb4_danish_ci        | utf8mb4 | 235 |         | Yes      |       8 |
| utf8mb4_lithuanian_ci    | utf8mb4 | 236 |         | Yes      |       8 |
| utf8mb4_slovak_ci        | utf8mb4 | 237 |         | Yes      |       8 |
| utf8mb4_spanish2_ci      | utf8mb4 | 238 |         | Yes      |       8 |
| utf8mb4_roman_ci         | utf8mb4 | 239 |         | Yes      |       8 |
| utf8mb4_persian_ci       | utf8mb4 | 240 |         | Yes      |       8 |
| utf8mb4_esperanto_ci     | utf8mb4 | 241 |         | Yes      |       8 |
| utf8mb4_hungarian_ci     | utf8mb4 | 242 |         | Yes      |       8 |
| utf8mb4_sinhala_ci       | utf8mb4 | 243 |         | Yes      |       8 |
+--------------------------+---------+-----+---------+----------+---------+
```

## 调整字符集方式

### 调整当前客户端字符集

**语法格式**
 set names [character set]

**在登录时调整字符集**

**语法格式**
	mysql -u[user] -p[password] --default-character-set=[character set]

#### 实战演练set names更改字符集方式

- 创建一个数据库为名称为db

```bash
mysql> create database db;
Query OK, 1 row affected (0.00 sec)
```

- 查看db库字符集

```bash
mysql> show create database db;
+----------+-------------------------------------------------------------+
| Database | Create Database                                             |
+----------+-------------------------------------------------------------+
| db       | CREATE DATABASE `db` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+-------------------------------------------------------------+
1 row in set (0.00 sec)
```

- 进入库db，创建一个表名称为db_table

```bash
mysql> use db
Database changed
mysql> create table db_table(id int(4),name char(10));
Query OK, 0 rows affected (0.01 sec)
```

- 查看表db_table的字符集

```bash
mysql> show create table db_table\G
*************************** 1. row ***************************
       Table: db_table
Create Table: CREATE TABLE `db_table` (
  `id` int(4) DEFAULT NULL,
  `name` char(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

- 往字段id和name插入内容

```bash
mysql> insert into db_table(id,name) values(1,'张三');
Query OK, 1 row affected (0.00 sec)
```

- 查看刚插入的内容，现在我们发现并没有乱码

```bash
mysql> select * from db_table;
+------+--------+
| id   | name   |
+------+--------+
|    1 | 张三   |
+------+--------+
1 row in set (0.00 sec)
```

- 修改当前的字符集为gbk

```bash
mysql> set names gbk;
Query OK, 0 rows affected (0.00 sec)
```

- 查看当前数据库的字符集设置情况，我们发现客户端的三个选项都变成了GBK的字符集

```bash
mysql> show variables like 'character_set%';
+--------------------------+-------------------------------------------+
| Variable_name            | Value                                     |
+--------------------------+-------------------------------------------+
| character_set_client     | gbk                                       |
| character_set_connection | gbk                                       |
| character_set_database   | utf8                                      |
| character_set_filesystem | binary                                    |
| character_set_results    | gbk                                       |
| character_set_server     | utf8                                      |
| character_set_system     | utf8                                      |
| character_sets_dir       | /application/mysql-5.5.32/share/charsets/ |
+--------------------------+-------------------------------------------+
8 rows in set (0.00 sec)
```

- 往字段内插入内容

```bash
mysql> insert into db_table(id,name) values(2,'李四');
Query OK, 1 row affected (0.04 sec)
```

- 查看表内容，这是我们就发现乱码了

```bash
mysql> select * from db_table;
+------+--------+
| id   | name   |
+------+--------+
|    1 | օɽ       |
|    2 | 李四   |
+------+--------+
2 rows in set (0.03 sec)
```

- 再把字符集改为utf8

```bash
mysql> set names utf8;
Query OK, 0 rows affected (0.00 sec)
```

- 查看当前数据库的字符集设置情况

```bash
mysql> show variables like 'character_set%';
+--------------------------+-------------------------------------------+
| Variable_name            | Value                                     |
+--------------------------+-------------------------------------------+
| character_set_client     | utf8                                      |
| character_set_connection | utf8                                      |
| character_set_database   | utf8                                      |
| character_set_filesystem | binary                                    |
| character_set_results    | utf8                                      |
| character_set_server     | utf8                                      |
| character_set_system     | utf8                                      |
| character_sets_dir       | /application/mysql-5.5.32/share/charsets/ |
+--------------------------+-------------------------------------------+
8 rows in set (0.00 sec)
```

- 再查看表内容，我们发现刚才修改字符集为gbk时插入的内容已经乱码了

```bash
mysql> select * from db_table;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | 张三      |
|    2 | 鏉庡洓    |
+------+-----------+
2 rows in set (0.00 sec)
```
> **小结：**字符集不同我们创建或者查看的内容都会变成乱码，而set names只是修改当前的字符集情况

#### 执行SQL文件插入中文数据不乱码方法

- 在执行SQL语句之前更改字符集

```bash
[root@MySQL ~]# cat db.sql 
SET NAMES UTF8；
CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `db`;
DROP TABLE IF EXISTS `db_table`;
CREATE TABLE `db_table` (
  `id` int(4) DEFAULT NULL,
  `name` char(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
LOCK TABLES `db_table` WRITE;
INSERT INTO `db_table` VALUES (1,'张三'),(2,'鏉庡洓');
UNLOCK TABLES;
```

- SQL语句导入的时候指定字符集

```bash
[root@MySQL ~]# mysql -uroot -p123456 --default-character-set=utf8 oldboy < db.sql
```

### 永久更改字符集

#### 通过修改更my.cnf实现修改mysql客户端的字符集

- 客户端

```bash
[client]
character-set-server=utf8
```

- 服务端

```bash
[mysqld]
character-set-server=utf8  --适合5.5
default-characyer-set=utf8 --适合5.1
```

#### 命令方式永久更改字符集

- 修改数据库库默认编码

```bash
help alter database
alter database [your db name] charset [your character settting]
mysql> show character set;
```

下面方法只能适应新数据，老数据字符集没有更改，库内的表也没有改

```bash
mysql> alter database oldboy CHARACTER SET latin1 COLLATE = latin1_swedish_ci;
mysql> show create database oldboy\g
```

更改表

```bash
mysql> show create table test\G
```

### 统一MySQL数据库客户及服务端字符集总结

1. 客户端字符集设置为“set names utf8”，这样可以确保插入后的中文，不出现乱码，但是对执行set names utf8;前插入的中文无效，此命令临时生效
2. 和设置客户端字符集“set names utf8”命令有相同作用的方法还有，mysql命令指定utf8字符集参数登录，以及在my.cnf里更改参数实现。
3. 在MySQL的my.cnf配置文件里的[client]模块下添加字符集配置，生效后，相当于命令行“set names utf8；”的效果，由于更改的是客户端、连接和返回结果三个字符集，应此无需重启服务就生效。
4. 在MySQL的my.cnf配置文嘉你的[mysqld]模块下添加字符集配置，生效后，创建数据库和表默认都是这个设置的字符集，MySQL5.5和5.1的服务端字符集参数有变化，具体为character-set-server=utf8参数适合5.5，default-character-set=utf8参数适合5.1及以前版本。
5. 不乱码的思想就是Linux服务器和MySQL数据库，以及创建的库和表的字符集都保持一致，还有客户端CRT或者Xshell的字符集也要保持一致。

### 更改字符集总的思想

1. 数据库不要更新，到处所有数据。
2. 把导出的数据进行字符集替换。
3. 修改my.cnf，更改MySQL客户端服务端字符集，重启生效。
4. 到处更改过字符集的数据包括表结构语句，提供服务。

## 如何调整已有MySQL数据库的字符集，例如：从UTF8改成GBK

- 查看当前系统的字符集设置

```bash
mysql> show variables like 'character%';
+--------------------------+-------------------------------------------+
| Variable_name            | Value                                     |
+--------------------------+-------------------------------------------+
| character_set_client     | utf8                                      |
| character_set_connection | utf8                                      |
| character_set_database   | utf8                                      |
| character_set_filesystem | binary                                    |
| character_set_results    | utf8                                      |
| character_set_server     | utf8                                      |
| character_set_system     | utf8                                      |
| character_sets_dir       | /application/mysql-5.5.32/share/charsets/ |
+--------------------------+-------------------------------------------+
#可以看到客户端服务端和服务器的字符集都是utf8
```

- 查看db库和表的字符集,这里就以一个数据库为例子，如果有很多数据库和表可以用脚本实现

```bash
mysql> mysql -uroot -p3306 -S /data/3306/mysql.sock -e "show create database db;
+----------+-------------------------------------------------------------+
| Database | Create Database                                             |
+----------+-------------------------------------------------------------+
| db       | CREATE DATABASE `db` /*!40100 DEFAULT CHARACTER SET utf8 */ |  --表的字符集为utf8
+----------+-------------------------------------------------------------+
mysql> show create table db.test\G
*************************** 1. row ***************************
       Table: test
Create Table: CREATE TABLE `test` (
  `id` int(4) DEFAULT NULL,
  `name` char(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8			--库的字符集为utf8
1 row in set (0.00 sec)
```

- 查看表的内容

```bash
mysql> select * from db.test;
+------+--------+
| id   | name   |
+------+--------+
|    1 | sa     |
|    2 | 测试   |			---中文并没有乱码
+------+--------+
2 rows in set (0.00 sec)
```

- 把数据库db以SQL语句的形式导出

```bash
[root@MySQL ~]# mysqldump -uroot -p3306 -S /data/3306/mysql.sock -B db>db.sql
[root@MySQL ~]# egrep -v "^-|^\/|^$" db.sql 
CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `db`;
DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
  `id` int(4) DEFAULT NULL,
  `name` char(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
LOCK TABLES `test` WRITE;
INSERT INTO `test` VALUES (1,'sa'),(2,'测试');
UNLOCK TABLES;
#现在可以看到有很多utf8的字符，这是都是自创建表的时候指定的字符集
```

- 修改备份文件的SQL语句utf8改为gbk

```bash
[root@MySQL ~]# cp db.sql db_bak.sql		#先做备份
[root@MySQL ~]# sed -i 's#utf8#gbk#g' db.sql   #把utf8全部改成gbk
[root@MySQL ~]# egrep -v "^-|^\/|^$" db.sql 
CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db` /*!40100 DEFAULT CHARACTER SET gbk */;
USE `db`;
DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
  `id` int(4) DEFAULT NULL,
  `name` char(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=gbk;
LOCK TABLES `test` WRITE;
INSERT INTO `test` VALUES (1,'sa'),(2,'测试');
UNLOCK TABLES;
#可以看到现在的SQL语句字符集是GBK
```

- 修改配置文件my.cnf指定客户端和服务端字符集为gbk

```bash
[root@MySQL ~]# vim /data/3306/my.cnf
[client]
character-set-server=gbk
[mysqld]
character-set-server=gbk
#在客户端和服务端模块中指定字符集为gbk
```

- 更改系统的字符集为GBK

```bash
[root@MySQL ~]# vim /etc/sysconfig/i18n 
#LANG="en_US.UTF-8"
LANG="zh_CN.GBK"
#LANG="zh_CN.UTF-8"
SYSFONT="latarcyrheb-sun16"
[root@MySQL ~]# reboot
#原有的字符集注销掉不删除，以免出错
```

- 修改客户端Xshell字符集为gbk

![mysql-crt](https://blog.ansheng.me/static/uploads/2016/12/1483011233891.png "mysql-crt")

- 删除数据库中的所有数据

```bash
mysql> drop database db;
Query OK, 1 row affected (0.07 sec)

mysql> show databases;
+######--+
| Database           |
+######--+
| information_schema |
| mysql              |
| performance_schema |
+######--+
3 rows in set (0.03 sec)
#这里只是测试，所以就只删除了db库
```

- 导入mysql的所有数据

```bash
[root@MySQL ~]# mysql -uroot -p3306 -S /data/3306/mysql.sock < db.sql
```

- 查看字符集及数据

```bash
mysql> show variables like 'character%';
+########--+##############-+
| Variable_name            | Value                                     |
+########--+##############-+
| character_set_client     | gbk                                       |
| character_set_connection | gbk                                       |
| character_set_database   | gbk                                       |
| character_set_filesystem | binary                                    |
| character_set_results    | gbk                                       |
| character_set_server     | gbk                                       |
| character_set_system     | gbk                                       |
| character_sets_dir       | /application/mysql-5.5.32/share/charsets/ |
+########--+##############-+
8 rows in set (0.00 sec)
#现在系统的字符集都变成了gbk了

mysql> show create database db;
+###-+####################+
| Database | Create Database                                            |
+###-+####################+
| db       | CREATE DATABASE `db` /*!40100 DEFAULT CHARACTER SET gbk */ |
+###-+####################+
1 row in set (0.00 sec)
#db库的字符集现在也是gbk了

mysql> show create table db.test\G
*************************** 1. row ***************************
       Table: test
Create Table: CREATE TABLE `test` (
  `id` int(4) DEFAULT NULL,
  `name` char(16) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=gbk
1 row in set (0.00 sec)
#表的字符集也是gbk

mysql> show databases;
+######--+
| Database           |
+######--+
| information_schema |
| db                 |
| mysql              |
| performance_schema |
+######--+
4 rows in set (0.00 sec)
#查看有数据库db

mysql> use db
Database changed
mysql> show tables;
+####--+
| Tables_in_db |
+####--+
| test         |
+####--+
1 row in set (0.00 sec)
#test表也存在

mysql> select * from db.test;
+##+##--+
| id   | name   |
+##+##--+
|    1 | sa     |
|    2 | 测试 |
+##+##--+
2 rows in set (0.00 sec)
#查看表内容中文也没有乱码
```

总结：utf8 >> gbk

1. 建库及建表的语句导出,sed批量修改为utf8
2. 带出所有mysql数据
3. 修改mysql服务端和客户端编码为utf8
4. 删除原有的库表及数据
5. 导入新的建库及建表的语句
6. 导入mysql的所有数据