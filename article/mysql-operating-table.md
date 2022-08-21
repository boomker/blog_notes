# Python全栈之路系列之MySQL表内操作

先创创建一个表用于测试

```sql
-- 创建数据库
CREATE DATABASE dbname DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
-- 创建表
CREATE TABLE `tb` (
  `id` int(5) NOT NULL AUTO_INCREMENT,
  `name` char(15) NOT NULL,
  `alias` varchar(10) DEFAULT NULL,
  `email` varchar(30) DEFAULT NULL,
  `password` varchar(20) NOT NULL,
  `phone` char(11) DEFAULT '13800138000',
  PRIMARY KEY (`id`,`name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## 增加表内数据

```bash
# 进入dbname数据库
mysql> use dbname
Database changed
# 查看当前库所有的表
mysql> show tables;
+------------------+
| Tables_in_dbname |
+------------------+
| tb               |
+------------------+
1 row in set (0.00 sec)
# 查看tb表内的内容
mysql> select * from tb;
Empty set (0.00 sec)
```

```sql
-- 插入单条数据
insert into tb(name,email,password) values("ansheng","anshengme.com@gmail.com","as");
-- 同时插入多条数据
insert into tb(name,email,password) values("as","i@anshengme.com","pwd"),("info","info@anshengme.com","i");
```
查看插入的数据
```bash
mysql> select * from tb;
+----+---------+-------+-------------------------+----------+-------------+
| id | name    | alias | email                   | password | phone       |
+----+---------+-------+-------------------------+----------+-------------+
|  2 | ansheng | NULL  | anshengme.com@gmail.com | as       | 13800138000 |
|  3 | as      | NULL  | i@anshengme.com         | pwd      | 13800138000 |
|  4 | info    | NULL  | info@anshengme.com      | i        | 13800138000 |
+----+---------+-------+-------------------------+----------+-------------+
3 rows in set (0.00 sec)
```

把别的表的数据插入当前表

查看tb_copy表内的内容
```bash
mysql> select * from tb_copy;
+----+--------+-------+-------+----------+-------------+
| id | name   | alias | email | password | phone       |
+----+--------+-------+-------+----------+-------------+
|  5 | hello  | NULL  | NULL  | 1        | 13800138000 |
|  6 | word   | NULL  | NULL  | 2        | 13800138000 |
|  7 | python | NULL  | NULL  | 3        | 13800138000 |
+----+--------+-------+-------+----------+-------------+
3 rows in set (0.00 sec)
```
把tb_copy表内的name,email,password列插入到tb表中
```sql
insert into tb (name, email, password) select name,email,password from tb_copy;
```
查询tb内的内容
```bash
mysql> select * from tb;
+----+---------+-------+-------------------------+----------+-------------+
| id | name    | alias | email                   | password | phone       |
+----+---------+-------+-------------------------+----------+-------------+
|  2 | ansheng | NULL  | anshengme.com@gmail.com | as       | 13800138000 |
|  3 | as      | NULL  | i@anshengme.com         | pwd      | 13800138000 |
|  4 | info    | NULL  | info@anshengme.com      | i        | 13800138000 |
|  5 | hello   | NULL  | NULL                    | 1        | 13800138000 |
|  6 | word    | NULL  | NULL                    | 2        | 13800138000 |
|  7 | python  | NULL  | NULL                    | 3        | 13800138000 |
+----+---------+-------+-------------------------+----------+-------------+
6 rows in set (0.00 sec)
```

## 删除表内数据

```sql
-- 删除表内的所有内容
delete from tb_copy;
```

```sql
-- 删除表内某一条数据
delete from tb where id=2 and name="ansheng";
```

## 更改表内数据

```sql
update tb set name="as" where id="3";
```

## 查

```sql
-- 查询表内所有内容
select * from tb;
-- 带条件的查询表内的内容
select * from tb where id > 4;
```
查询的时候指定最后一列的名称
```sql
mysql> select id,name as username from tb where id > 4;
+----+----------+
| id | username |
+----+----------+
|  5 | hello    |
|  6 | word     |
|  7 | python   |
+----+----------+
3 rows in set (0.00 sec)
```

## 其他操作

条件

```sql
-- 多条件查询
select * from tb where id>3 and name="hello" and password="1";
-- 查询指定范围
select * from tb where id between 4 and 6;
-- 查询括号内存在的数据
select * from tb where id in (4,6);
-- 查询括号内不存在的数据
select * from tb where id not in (4,6);
-- 以别的表的内容为查询条件
select * from tb where id in (select id from tb_copy);
```

通配符

```sql
-- 以p开头的所有（多个字符串）
select * from tb where name like "p%";
-- 以p开头的所有（一个字符）
select * from tb where name like "p%";
```

限制

```sql
-- 前三行数据
select * from tb limit 3;
-- 从第2行开始的3行
select * from tb limit 2,3;
-- 从第4行开始的5行
select * from tb limit 5 offset 4;
```

排序

```sql
-- 根据"name"列从小到大排列
select * from tb order by name asc;
-- 根据"name"列从大到小排列
select * from tb order by name desc;
-- 根据 “列1” 从大到小排列，如果相同则按列2从小到大排序
select * from 表 order by 列1 desc,列2 asc;
```

分组

```sql
select id from tb group by id;
select id,name from tb group by id,name;
select num,nid from 表  where nid > 10 group by num,nid order nid desc;
select num,nid,count(*),sum(score),max(score),min(score) from 表 group by num,nid;
select num from 表 group by num having max(id) > 10;
```

特别的：group by 必须在where之后，order by之前

连表

无对应关系则不显示

```sql
select A.num, A.name, B.name from A,B where A.nid = B.nid;
```

无对应关系则不显示

```sql
select A.num, A.name, B.name from A inner join B on A.nid = B.nid;
```

A表所有显示，如果B中无对应关系，则值为null

```sql
select A.num, A.name, B.name from A left join B on A.nid = B.nid;
```

B表所有显示，如果B中无对应关系，则值为null

```sql
select A.num, A.name, B.name from A right join B on A.nid = B.nid;
```

组合

组合，自动处理重合

```sql
select nickname from A union select name from B;
```

组合，不处理重合

```sql
select nickname from A union all select name from B;
```