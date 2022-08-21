# Python全栈之路系列之MySQL连表查询

普通的连表查询，把一个`select`的结果当作另外一个`select`的参数

```sql
SELECT * FROM personnel.person_info where personnel.person_info.part_nid in (SELECT nid from personnel.part WHERE personnel.part.caption="XO股份有限公司公司-技术部-Python开发");
```

显示结果
```bsh
+-----+---------+------------------+-------------+----------+----------+---------+
| nid | name    | email            | phone       | part_nid | position | caption |
+-----+---------+------------------+-------------+----------+----------+---------+
|   1 | as      | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |
|   2 | ansheng | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |
|   3 | a       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |
|   4 | v       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |
|   5 | b       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |
|   6 | w       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |
+-----+---------+------------------+-------------+----------+----------+---------+
6 rows in set (0.00 sec)
```

## join进行连表查询
SQL指令
```sql
select * from personnel.person_info left join personnel.part on personnel.person_info.part_nid = personnel.part.nid where personnel.part.caption = 'XO股份有限公司公司-技术部-Python开发';
```
显示结果
```sql
+-----+---------+------------------+-------------+----------+----------+---------+------+---------------------------------------------------+
| nid | name    | email            | phone       | part_nid | position | caption | nid  | caption                                           |
+-----+---------+------------------+-------------+----------+----------+---------+------+---------------------------------------------------+
|   1 | as      | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |    5 | XO股份有限公司公司-技术部-Python开发              |
|   2 | ansheng | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |    5 | XO股份有限公司公司-技术部-Python开发              |
|   3 | a       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |    5 | XO股份有限公司公司-技术部-Python开发              |
|   4 | v       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |    5 | XO股份有限公司公司-技术部-Python开发              |
|   5 | b       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |    5 | XO股份有限公司公司-技术部-Python开发              |
|   6 | w       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |    5 | XO股份有限公司公司-技术部-Python开发              |
+-----+---------+------------------+-------------+----------+----------+---------+------+---------------------------------------------------+
6 rows in set (0.00 sec)
```

上面的意思是查询`personnel.person_info.part_nid`列的字段和`personnel.part.nid`一样的数据，并且`personnel.part.caption = 'XO股份有限公司公司-技术部-Python开发'`

|参数|实例|描述|
|:--|:--|:--|
|`left join`|`tb1 left join tb2`|左外链接|
|`right join`|`tb1 right join tb2`|右外链接|
|`inner join`|`tb1 inner join tb2`|内链接|
|`Full Join`|-|全外连接|
|`CROSS`|-|交叉链接，又称笛卡尔链接或叉乘|

**left join**

1. tb1为主，tb2为辅，将A中所有的数据罗列出来
2. tb2则只显示与tb1对应的数据

执行以下语句在`person_info`表中插入一条数据

```sql
INSERT INTO personnel.person_info (NAME,email,phone,part_nid,position) VALUES("aa","a@ansheng.me",13800138000,3,"DBA");
```
通过`left jion`进行查询
```sql
mysql> use personnel
Database changed
mysql> select * from person_info LEFT JOIN part on person_info.part_nid = part.nid WHERE part.nid = 3;
+-----+------+--------------+-------------+----------+----------+---------+------+------------------------------------------+
| nid | name | email        | phone       | part_nid | position | caption | nid  | caption                                  |
+-----+------+--------------+-------------+----------+----------+---------+------+------------------------------------------+
|   9 | aa   | a@ansheng.me | 13800138000 |        3 | DBA      | NULL    |    3 | XO股份有限公司公司-技术部-DBA            |
+-----+------+--------------+-------------+----------+----------+---------+------+------------------------------------------+
1 row in set (0.00 sec)
```
这样他就只把我们刚刚插入的那条数据查询了除了，即查询`person_info`表中的内容，`part`表中的列作为`person_info`表的查询条件，如果`person_info`表中的`part_nid`列如果等于`part`表中`nid`列，那么就显示数据。

**inner join**

1. 自动忽略两张表没有建立关联数据
2. 只返回两个表中链接字段相等的数据

```sql
select * from person_info inner JOIN part on person_info.part_nid = part.nid;
```
显示结果
```bash
+-----+---------+------------------+-------------+----------+----------+---------+-----+---------------------------------------------------+
| nid | name    | email            | phone       | part_nid | position | caption | nid | caption                                           |
+-----+---------+------------------+-------------+----------+----------+---------+-----+---------------------------------------------------+
|   1 | as      | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |   5 | XO股份有限公司公司-技术部-Python开发              |
|   2 | ansheng | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |   5 | XO股份有限公司公司-技术部-Python开发              |
|   3 | a       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |   5 | XO股份有限公司公司-技术部-Python开发              |
|   4 | v       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |   5 | XO股份有限公司公司-技术部-Python开发              |
|   5 | b       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |   5 | XO股份有限公司公司-技术部-Python开发              |
|   6 | w       | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |   5 | XO股份有限公司公司-技术部-Python开发              |
|   9 | aa      | a@ansheng.me     | 13800138000 |        3 | DBA      | NULL    |   3 | XO股份有限公司公司-技术部-DBA                     |
+-----+---------+------------------+-------------+----------+----------+---------+-----+---------------------------------------------------+
7 rows in set (0.00 sec)
```

**MySQL Full Join的实现**

1. 把左右两个表的数据都取出来，不管是否匹配

`MySQL Full Join`的实现 因为MySQL不支持`FULL JOIN`,下面是替代方法

**语法**
```sql
select * from A left join B on A.id = B.id (where 条件）
union --all可选
select * from A right join B on A.id = B.id （where条件);
```

**CROSS**

如果A和B是两个集合，他们的交叉连接就记为：AxB

```bash
mysql> use personnel
Database changed
mysql> SELECT * from course;
+-----+--------+
| Cno | Cname  |
+-----+--------+
|   1 | 足球   |
|   2 | 篮球   |
|   3 | 排球   |
+-----+--------+
3 rows in set (0.00 sec)

mysql> SELECT * from student;
+-----+--------+
| Sno | Name   |
+-----+--------+
|   1 | 张三   |
|   2 | 李四   |
|   3 | 王五   |
+-----+--------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM course CROSS JOIN student;
+-----+--------+-----+--------+
| Cno | Cname  | Sno | Name   |
+-----+--------+-----+--------+
|   1 | 足球   |   1 | 张三   |
|   2 | 篮球   |   1 | 张三   |
|   3 | 排球   |   1 | 张三   |
|   1 | 足球   |   2 | 李四   |
|   2 | 篮球   |   2 | 李四   |
|   3 | 排球   |   2 | 李四   |
|   1 | 足球   |   3 | 王五   |
|   2 | 篮球   |   3 | 王五   |
|   3 | 排球   |   3 | 王五   |
+-----+--------+-----+--------+
9 rows in set (0.00 sec)
```

## 一对多，多表查询

创建`color`表
```sql
CREATE TABLE `color` (
  `nid` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`nid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
往`color`表中添加两条数据
```sql
insert into color(title) values('red'),('yellow');
```
`person_info`表添加`color_nid`列，类型是`int`
```sql
alter table person_info add color_nid int;
```
把`person_info`表中的`color_nid`列和`color`表中的`nid`列做一个外键关联
```sql
alter table person_info add constraint person_ibfk_2 foreign key person_info(`color_nid`) REFERENCES color(`nid`);
```
往`person_info`表中插入一条数据
```sql
INSERT INTO personnel.person_info (NAME,email,phone,part_nid,position,color_nid) VALUES("b", "b.ansheng.me",13800138000,3,"DBA",1)
```
查询职位是`XO股份有限公司公司-技术部-DBA`，颜色是`yellow`的的人员
```sql
SELECT * FROM person_info LEFT JOIN part ON person_info.part_nid = part.nid LEFT JOIN color ON person_info.color_nid = color.nid WHERE color.title = "yellow";
```

## 多对多关系及查询

先创建三张表

student(学生表)

```sql
CREATE TABLE `student` (
  `Sno` int(11) NOT NULL AUTO_INCREMENT,
  `Name` char(20) NOT NULL,
  PRIMARY KEY (`Sno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

 course(课程表)

```sql
CREATE TABLE `course` (
  `Cno` int(11) NOT NULL AUTO_INCREMENT,
  `Cname` char(10) NOT NULL,
  PRIMARY KEY (`Cno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

student_course(关系表)

```sql
CREATE TABLE `student_course` (
  `ID` int(11) NOT NULL AUTO_INCREMENT,
  `Sno` int(11) NOT NULL,
  `Cno` int(11) NOT NULL,
  PRIMARY KEY (`ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
设置外键关联
```sql
ALTER TABLE student_course ADD CONSTRAINT student_course_to_student_ibfk_2 FOREIGN KEY student_course (`Sno`) REFERENCES student (`Sno`);
ALTER TABLE student_course ADD CONSTRAINT student_course_to_course_ibfk_1 FOREIGN KEY student_course (`Cno`) REFERENCES course (`Cno`);
```
往`course(课程表)`中插入数据
```sql
INSERT INTO course(Cname) VALUES("足球"),("篮球"),("排球");
```
往`student(学生表)`中插入数据
```sql
INSERT INTO student(Name) VALUES("张三"),("李四"),("王五");
```
`student_course(关系表)`插入数据
```sql
INSERT INTO student_course(Sno,Cno) VALUES(2,1),(2,3),(3,3),(3,1),(1,2),(3,2);
```
显示学生所选的课程SQL指令
```sql
SELECT s.Name,C.Cname FROM student_course AS sc LEFT JOIN student AS s ON s.Sno=sc.Sno LEFT JOIN course AS c ON c.Cno=sc.Cno;
```
结果如下:
```bash
+--------+--------+
| Name   | Cname  |
+--------+--------+
| 张三   | 篮球   |
| 李四   | 足球   |
| 李四   | 排球   |
| 王五   | 排球   |
| 王五   | 足球   |
| 王五   | 篮球   |
+--------+--------+
6 rows in set (0.00 sec)
```