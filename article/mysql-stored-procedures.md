# Python全栈之路系列之MySQL存储过程

存储过程是一个SQL语句集合，当主动去调用存储过程时，其中内部的SQL语句会按照逻辑执行。

存储过程过接收的参数

|参数|描述|
|:--|:--|
|`in`|仅用于传入参数用|
|`out`|仅用于返回值用|
|`inout`|既可以传入又可以当作返回值|

创建存储过程

创建一个简单的存储过程

```sql
-- 修改SQL语句的结束符为%
delimiter %
-- 创建这个存储过程先删除
DROP PROCEDURE IF EXISTS proc_p1 %
CREATE PROCEDURE proc_p1()
-- 开始
BEGIN
    -- SQL语句块
    select * from color;
-- 结束
END %
-- 把SQL语句的结束符改为;
delimiter ;
```

通过`call`调用存储过程

```sql
call proc_p1();
```
输出为
```bash
+-----+--------+
| nid | title  |
+-----+--------+
|   1 | red    |
|   2 | yellow |
+-----+--------+
2 rows in set (0.00 sec)

Query OK, 0 rows affected (0.01 sec)
```

删除存储过程

```sql
DROP PROCEDURE proc_p1;
```

## 实例

创建一个存储过程，接收一个参数，传入的参数就是显示数据的个数，

```sql
delimiter %
DROP PROCEDURE IF EXISTS proc_p1 %
create PROCEDURE proc_p1(
	-- i1就是传入的参数，传入的数据类型必须是int类型
	in i1 int
)
BEGIN
	-- 定义两个局部变量d1和d2，数据类型都为int，d1默认值为空，d2默认值为1
	DECLARE d1 int;
	DECLARE d2 int DEFAULT 1;
	-- d1的值等于传入过来的i1加上定义的局部变量d2的值
	SET d1 = i1 + d2;
	-- 查找person_info表中的nid大于d1的数据
	SELECT * FROM person_info WHERE nid > d1;
END %
delimiter ;
```
查询，括号内输入定义的参数
```
CALL proc_p1(4);
```
显示结果
```bash
+-----+------+------------------+-------------+----------+----------+---------+-----------+
| nid | name | email            | phone       | part_nid | position | caption | color_nid |
+-----+------+------------------+-------------+----------+----------+---------+-----------+
|   6 | w    | as@anshengme.com | 13800138000 |        5 | Python   | NULL    |      NULL |
|   9 | aa   | a@ansheng.me     | 13800138000 |        3 | DBA      | NULL    |         2 |
|  10 | b    | b.ansheng.me     | 13800138000 |        3 | DBA      | NULL    |         1 |
+-----+------+------------------+-------------+----------+----------+---------+-----------+
3 rows in set (0.00 sec)

Query OK, 0 rows affected (0.01 sec)
```
这次把`nid`大于`5`的数据全部输出出来了，传入的值是`4`，我们在内部让`4+1`了，所以就是大于`5`的数据.

```sql
delimiter %
DROP PROCEDURE IF EXISTS proc_p1 %
create PROCEDURE proc_p1(
	-- 接收了三个参数，类型都是int
	in i1 int,
	inout ii int,
	out i2 int
)
BEGIN
	-- 定义一个局部变量d2，默认值是3，数据类型为int
	DECLARE d2 int DEFAULT 3;
	-- ii = ii + 1
	set ii = ii + 1;
	-- 如果传入的i1等于1
	IF i1 = 1 THEN
		-- i2 = 100 + d2
		set i2 = 100 + d2;
	-- 如果传入的i1等于2
	ELSEIF i1 = 2 THEN
        -- i2 = 200 + d2
        set i2 = 200 + d2;
	-- 否则
	ELSE
	    -- i2 = 1000 + d2
        set i2 = 1000 + d2;
	END IF;
END %
delimiter ;
```
查看数据
```sql
set @o = 5;
CALL proc_p1(1,@o,@u);
SELECT @o,@u;
```
显示的结果
```bash
+------+------+
| @o   | @u   |
+------+------+
|    6 |  103 |
+------+------+
1 row in set (0.00 sec)
```

使用pymysql模块操作存储过程

Python代码为：

```python
import pymysql

conn = pymysql.connect(host="127.0.0.1", port=3306, user='root', passwd='as', db="dbname")
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)

# 执行存储过程
row = cursor.callproc("proc_p2",(1,2,3))
# 存储过程的查询结果
selc = cursor.fetchall()
print(selc)
# 获取存储过程返回
effect_row = cursor.execute('select @_proc_p2_0, @_proc_p2_1, @_proc_p2_2')
# 取存储过程返回值
result = cursor.fetchone()
print(result)

conn.commit()
cursor.close()
conn.close()
```
显示的结果
```bash
C:\Python\Python35\python.exe D:/PycharmProjects/pymysql_存储过程.py
[{'nid': 1, 'name': 'man1'}, {'nid': 2, 'name': 'man2'}, {'nid': 3, 'name': 'man3'}]
{'@_proc_p2_1': 3, '@_proc_p2_0': 1, '@_proc_p2_2': 103}

Process finished with exit code 0
```

存储过程使用into

`into`其实就是把一个`select`的执行结果当作另一个`select`的参数，例如下面的实例：

```sql
delimiter %
DROP PROCEDURE IF EXISTS proc_p2 %
CREATE PROCEDURE proc_p2()
BEGIN
	-- 定义一个局部变量n，类型为int
	DECLARE n int;
	-- 获取color_nid = 2的数据并赋值给n
	SELECT color_nid into n FROM person_info where color_nid = 2;
	-- 输出nid = n的数据
	SELECT * from color WHERE nid = n;
END %
delimiter ;
```
执行
```
call proc_p2();
```
结果
```bash
+-----+--------+
| nid | title  |
+-----+--------+
|   2 | yellow |
+-----+--------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.01 sec)
```