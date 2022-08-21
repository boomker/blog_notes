# 那些MySQL必会的基础命令（二）

1.登陆数据库

单实例

```bash
[root@MySQL ~]# mysql -uroot -p123456
```

多实例

```bash
[root@MySQL ~]# mysql -uroot -p123456 -S /data/3306/mysql.sock
```

 2.查看数据库版本及当前登录用户是什么 

查看数据库版本

```bash
[root@MySQL ~]# mysql -uroot -p3307 -S /data/3307/mysql.sock
select version();
```

查看当前登陆用户

```bash
select user();
```

 3.创建 GBK 字符集的数据库 AnSheng，并查看已建库的完整语句。

创建库

```bash
create database AnSheng CHARACTER SET gbk COLLATE gbk_chinese_ci;
```

查看语句

```bash
show create database confeseur;
```

 4.创建用户AnSheng， 使之可以管理数据库AnSheng 

```bash
create user 'AnSheng'@'localhost' identified by '123465';
grant all on AnSheng.* to 'AnSheng'@'localhost';
```

 5.查看创建的用户 AnSheng 拥有哪些权限 

```bash
SHOW GRANTS FOR 'AnSheng'@'localhost';
```

 6.查看当前数据库里有哪些用户 

```bash
select user,host from mysql.user;
```

 7.进入AnSheng数据库 

```bash
use AnSheng
select database();
```

 8.创建一个innodb引擎字符集为GBK表test，字段为id和name varchar(16)，查看建表结构及SQL 语句

```bash
use AnSheng
CREATE TABLE test(`id` int(4) NOT NULL,`name` char(20) NOT NULL) ENGINE=InnoDB DEFAULT CHARSET=utf8;
show create table test\G
```

9.插入一条数据 1,AnSheng

```bash
insert into test(id,name) values(1,'AnSheng');
select * from test;
```

10.批量插入数据 2,CON， 3,111。 要求中文不能乱码

```bash
insert into test values(1,'CON'),(3,'111');
select * from test;
```

11.查询插入的所有记录，查询名字为AnSheng的记录。查询id大于1的记录

```bash
select id,name from AnSheng.test where name = 'AnSheng';
select id,name from AnSheng.test where id1;
```

12.把数据 id 等于 1 的名字 AnSheng 更改为 www

```bash
update AnSheng.test set name='www' where id = '1' and name = 'AnSheng';
```

13.在字段 name 前插入 age 字段， 类型 int(4)

```bash
alter table test add age int(4) after id;
desc test;
```

14.备份 AnSheng 库及 MySQL 库

```bash
[root@MySQL ~]# mysqldump -uroot -p3306 -B AnSheng  AnSheng.sql
[root@MySQL ~]# mysqldump -uroot -p3306 -B --events mysql  mysql.sql
```

15.删除表中的所有数据， 并查看

```bash
delete from AnSheng.test;
use AnSheng
select * from test;
```

16.删除表 test 和 AnSheng 数据库并查看

```bash
drop table test;
use AnSheng
show tables;
drop database AnSheng;
show databases;
```

17.Linux 命令行恢复以上删除的数据

```bash
[root@MySQL ~]# mysql -uroot -p3306 &lt;AnSheng.sql
[root@MySQL ~]# mysql -uroot -p3306 -e "show databases;use AnSheng;show把 GBK 字符集修改为 UTF8（可选，注意，此题有陷阱）。
cp AnSheng.sql AnSheng.sql.bak
sed -i 's#GBK#UTF8#g' AnSheng.sql
mysql -uroot -p3306 &lt;AnSheng.sql
```

18.MySQL 密码丢了，如何找回实战？

```bash
killall mysqld
mysqld_safe --defaults-file=/data/3310/my.cnf --skip-grant-table &amp;
mysql -uroot -p -S /data/3310/mysql.sock
update mysql.user set password=password('123456') where user = 'root' and host = 'localhost';
flush privileges;
quit
/data/3310/mysql restart
mysql -uroot -p123456 -S /data/3310/mysql.sock
```

19.MySQL 内中文数据乱码的原理及如何防止乱码？ （可选）

20.在把 id 列设置为主键，在 Name 字段上创建普通索引

```bash
ALTER TABLE test ADD PRIMARY KEY (id);
desc test;
ALTER TABLE test ADD INDEX index_name (name);
```

21.在字段 name 后插入手机号字段(shouji)， 类型 char(11)

```bash
alter table test add shouji char(11) after name;
desc test;
```

22.所有字段上插入 2 条记录（自行设定数据）

```bash
insert into test(id,age,name,shouji) values(1,18,'sa',18002552466),(2,15,'natasha',18002552566);
select * from test where id = '2' or id = '1';
```

23.在手机字段上对前 8 个字符创建普通索引

```bash
create index index_shouji on test(shouji(8));
```

24.查看创建的索引及索引类型等信息

```bash
desc test;
```

25.删除 Name， shouji 列的索引

```bash
ALTER TABLE test DROP INDEX index_name;
ALTER TABLE test DROP INDEX index_shouji;
desc test;
```

26.对Name列的前 6 个字符以及手机列的前 8 个字符组建联合索引

```bash
create index ind_name_shouji on test(name,shouji);
```

27.查询手机号以 135 开头的，名字为 AnSheng 的记录（此记录要提前插入）

```bash
insert into test(id,name,shouji) values(10,'AnSheng',13565485236);
select * from test where name='AnSheng' and shouji like '135%';
```

28.查询上述语句的执行计划（是否使用联合索引 等）

```bash
explain select * from test where name='sa';
```