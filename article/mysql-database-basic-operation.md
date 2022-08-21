# Python全栈之路系列之MySQL数据库基本操作

## MySQL数据库介绍

`MySQL`是一种快速易用的`关系型数据库管理系统(RDBMS)`，很多企业都在使用它来构建自己的数据库。

MySQL由一家瑞典公司`MySQL AB`开发、运营并予以支持。它之所以非常流行，原因在于具备以下这些优点：

1. 基于开源许可发布，无需付费即可使用。
2. 自身的功能非常强大，足以匹敌绝大多数功能强大但却价格昂贵的数据库软件。
3. 使用业内所熟悉的标准SQL数据库语言。
4. 可运行于多个操作系统，支持多种语言，包括 PHP、PERL、C、C++ 及 Java 等语言。
5. 非常迅速，即使面对大型数据集也毫无滞涩。
6. 非常适用于 PHP 这种 Web 开发者最喜欢使用的语言。
7. 支持大型数据库，最高可在一个表中容纳 5千多万行。每张表的默认文件大小限制为 4GB，不过如果操作系统支持，你可以将其理论限制增加到 800 万 TB。
8. 可以自定义。开源 GPL 许可保证了程序员可以自由修改 MySQL，以便适应各自特殊的开发环境。

- 关系型数据库管理系统（RDBMS）具有以下特点：

1. 能够实现一种具有表、列与索引的数据库。
2. 保证不同表的行之间的引用完整性。
3. 能自动更新索引。
4. 能解释 SQL 查询，组合多张表的信息。

**RDBMS术语**

|术语|描述|
|:--|:--|
|`数据库（Database）`|数据库是带有相关数据的表的集合|
|`表（Table）`|表是带有数据的矩阵。数据库中的表就像一种简单的电子表格|
|`列（Column）`|每一列（数据元素）都包含着同种类型的数据，比如邮编|
|`行（Row）`|行（又被称为元组、项或记录）是一组相关数据，比如有关订阅量的数据|
|`冗余（Redundancy）`|存储两次数据，以便使系统更快速|
|`主键（Primary Key）`|主键是唯一的。同一张表中不允许出现同样两个键值。一个键值只对应着一行|
|`外键（Foreign Key）`|用于连接两张表|
|`复合键（Compound Key）`|复合键（又称组合键）是一种由多列组成的键，因为一列并不足以确定唯一性|
|`索引（Index）`|它在数据库中的作用就像书后的索引一样|
|`引用完性（Referential Integrity）`|用来确保外键一直指向已存在的一行|

## 安装MySQL数据库


连接已经启动的MySQL数据库指令

```bash
ansheng@Darker:~$ mysql -uroot -pas -h 192.168.56.1 -P 3306
```

## 数据库基本操作

查看当前的所有数据库

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| test               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

|默认数据库|描述|
|:--|:--|
|`information_schema`|提供了访问数据库元数据的方式,元数据如数据库名或表名，列的数据类型或访问权限等|
|`test`|用户用来测试的数据库库|
|`mysql`|用户权限相关数据|
|`performance_schema`|用于收集数据库服务器性能参数|
|`sys`|包含了一系列视图、函数和存储过程|

创建数据库

```SQL
-- 创建字符串为utf-8的数据库
CREATE DATABASE dbname DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

```sql
-- 创建字符串为gbk的数据库
CREATE DATABASE dbname DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;
```

进入数据库

```SQL
use dbname;
```

删除数据库

```sql
drop database dbname;
```

用户相关

创建`ansheng`用户,允许所有主机连接,密码设置为`as`
```sql
create user 'ansheng'@'%' identified by 'as';
```
修改`ansheng`用户的用户名为`as`
```sql
rename user 'ansheng'@'%' to 'anshengme'@'192.168.56.1';
```
修改`anshengme`用户的密码为`123`
```sql
set password for 'anshengme'@'192.168.56.1' = Password('123');
```
删除`anshengme`用户
```sql
drop user 'anshengme'@'192.168.56.1';
```

用户权限相关数据保存在mysql数据库的user表中，所以也可以直接对其进行操作（不建议）

用户授权

```sql
-- 查看权限
show grants for 'root'@'localhost';
-- 授权
grant 权限 on 数据库.表 to '用户'@'IP地址';
-- 取消权限
revoke 权限 on 数据库.表 from '用户'@'IP地址';
```

## 数据表基本操作

创建表

```sql
create table 表名(
    列名  类型  是否可以为空，
    列名  类型  是否可以为空
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```

是否可以为空

```sql
create table tb_name(
    `username_not`  varchar(30) NOT NULL ,    -- 不可空
    `username_null`  varchar(30) NULL         -- 可空
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```

默认值，创建列时可以指定默认值，当插入数据时如果未主动设置，则自动添加默认值

```sql
create table tb_name(
    nid int not null defalut 2,
    num int not null
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```

自增，如果为某列设置自增列，插入数据时无需设置此列，默认将自增（表中只能有一个自增列）
```sql
create table tb_name(
    nid int not null auto_increment primary key,
    num int null
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```
或
```sql
create table tb_name(
    nid int not null auto_increment,
    num int null,
    index(nid)
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```

1. 对于自增列，必须是索引（含主键）。
2. 对于自增可以设置步长和起始值

```sql
show session variables like 'auto_inc%';
set session auto_increment_increment=2;
set session auto_increment_offset=10;

shwo global variables like 'auto_inc%';
set global auto_increment_increment=2;
set global auto_increment_offset=10;
```

主键，一种特殊的唯一索引，不允许有空值，如果主键使用单个列，则它的值必须唯一，如果是多列，则其组合必须唯一。
```sql
create table tb1(
    nid int not null auto_increment primary key,
    num int null
)
```
或
```sql
create table tb1(
    nid int not null,
    num int not null,
    primary key(nid,num)
)
```

外键，一个特殊的索引，只能是指定内容

```sql
create table color(
    nid int not null primary key,
    name char(16) not null
)
```
```sql
create table fruit(
    nid int not null primary key,
    smt char(32) null ,
    color_id int not null,
    constraint fk_cc foreign key (color_id) references color(nid)
)
```

删除表

```sql
drop table tb_name;
```

清空表

```sql
-- 如果清空的表又自增列,那么在清空之后会继续上次自增的值继续自增
delete from tb_name;
-- 如果清空的表又自增列,那么在清空之后再次添加数据自增的值会从新开始计算
truncate table tb_name;
```

修改表

```sql
-- 添加列
alter table 表名 add 列名 类型;

-- 删除列
alter table 表名 drop column 列名;

-- 修改列
alter table 表名 modify column 列名 类型;  -- 类型
alter table 表名 change 原列名 新列名 类型; -- 列名，类型

-- 添加主键
alter table 表名 add primary key(列名);

-- 删除主键
alter table 表名 drop primary key;
alter table 表名  modify  列名 int, drop primary key;

-- 添加外键
alter table 从表 add constraint 外键名称（形如：FK_从表_主表） foreign key 从表(外键字段) references 主表(主键字段);

-- 删除外键
alter table 表名 drop foreign key 外键名称;

-- 修改默认值
ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;

-- 删除默认值
ALTER TABLE testalter_tbl ALTER i DROP DEFAULT;
```