# Python全栈之路系列之MySQL基本数据类型

MySQL中定义数据字段的类型对你数据库的优化是非常重要的。

MySQL支持多种类型，大致可以分为三类：

1. **数字类型**
2. **日期和时间类型**
3. **字符串类型**

## 数字类型

|类型|大小|用途|
|:--|:--|:--|
|`BIT`|-|二进制|
|`TINYINT`|1字节|小整数值|
|`INT or INTEGER`|4字节|大整数值|
|`BIGINT`|8字节|极大整数值|
|`DECIMAL`|对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2|小数值|
|`FLOAT`|4字节|单精度浮点数值|
|`DOUBLE`|8字节|双精度浮点数值|
|`BOOL, BOOLEAN`|-|布尔值|
- **BIT[(M)]**

二进制位（101001），m表示二进制位的长度（1-64），默认m＝1

- **TINYINT[(M)] [UNSIGNED] [ZEROFILL]**

小整数，数据类型用于保存一些范围的整数数值范围：

|范围（有符号）|范围（无符号）|
|:--|:--|
|`-128 to 127`|`0 to 255`|

特别的： MySQL中无布尔值，使用tinyint(1)构造。

- **INT[(M)] [UNSIGNED] [ZEROFILL]**

整数，数据类型用于保存一些范围的整数数值范围：

|范围（有符号）|范围（无符号）|
|:--|:--|
|`-2147483648 to 2147483647`|`0 to 4294967295`|

整数类型中的m仅用于显示，对存储范围无限制。例如： `int(5)`,当插入数据2时，`select`时数据显示为：`00002`

- **BIGINT[(M)] [UNSIGNED] [ZEROFILL]**

大整数，数据类型用于保存一些范围的整数数值范围：

|范围（有符号）|范围（无符号）|
|:--|:--|
|`-9223372036854775808 to 9223372036854775807`|`0  to  18446744073709551615`|

- **DECIMAL[(M[,D])] [UNSIGNED] [ZEROFILL]**

准确的小数值，m是数字总个数（负号不算），d是小数点后个数。 m最大值为65，d最大值为30。

特别的：对于精确数值计算时需要用此类型decaimal能够存储精确值的原因在于其内部按照字符串存储。

- **FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]**

单精度浮点数（非准确小数值），m是数字总个数，d是小数点后个数。

- 无符号：
    `-3.402823466E+38 to -1.175494351E-38,`
    `0`
    `1.175494351E-38 to 3.402823466E+38`
- 有符号：
    `0`
    `1.175494351E-38 to 3.402823466E+38`

**** 数值越大，越不准确 ****

- **DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL]**

双精度浮点数（非准确小数值），m是数字总个数，d是小数点后个数。

- 无符号：
    `-1.7976931348623157E+308 to -2.2250738585072014E-308`
    `0`
    `2.2250738585072014E-308 to 1.7976931348623157E+308`
- 有符号：
    `0`
    `2.2250738585072014E-308 to 1.7976931348623157E+308`

数值越大，越不准确

- **BOOL, BOOLEAN**

这些类型是`TINYINT`的同义词。零值被认为是假的。非零值被认为是正确的：

```sql
mysql> SELECT IF(0, 'true', 'false');
+------------------------+
| IF(0, 'true', 'false') |
+------------------------+
| false                  |
+------------------------+
1 row in set (0.00 sec)

mysql> SELECT IF(1, 'true', 'false');
+------------------------+
| IF(1, 'true', 'false') |
+------------------------+
| true                   |
+------------------------+
1 row in set (0.00 sec)

mysql> SELECT IF(2, 'true', 'false');
+------------------------+
| IF(2, 'true', 'false') |
+------------------------+
| true                   |
+------------------------+
1 row in set (0.00 sec)
```

然而，真假的值仅仅是为了分别为1和0，别名，如下所示：

```sql
mysql> SELECT IF(0 = FALSE, 'true', 'false');
+--------------------------------+
| IF(0 = FALSE, 'true', 'false') |
+--------------------------------+
| true                           |
+--------------------------------+
1 row in set (0.01 sec)

mysql> SELECT IF(1 = TRUE, 'true', 'false');
+-------------------------------+
| IF(1 = TRUE, 'true', 'false') |
+-------------------------------+
| true                          |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IF(2 = TRUE, 'true', 'false');
+-------------------------------+
| IF(2 = TRUE, 'true', 'false') |
+-------------------------------+
| false                         |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IF(2 = FALSE, 'true', 'false');
+--------------------------------+
| IF(2 = FALSE, 'true', 'false') |
+--------------------------------+
| false                          |
+--------------------------------+
1 row in set (0.00 sec)
```

## 时间类型

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。
每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。

|类型|大小(字节)|格式|用途|
|:--|:--|:--|:--|
|`DATE`|3|`YYYY-MM-DD`|日期值|
|`DATETIME`|8|`YYYY-MM-DD HH:MM:SS`|混合日期和时间值|
|`TIMESTAMP`|8|`YYYYMMDD HHMMSS`|混合日期和时间值，时间戳|
|`TIME`|3|`HH:MM:SS`|时间值或持续时间|
|`YEAR`|1|`YYYY`|年份值|

|类型|范围|
|:--|:--|
|`DATE`|`'1000-01-01' to '9999-12-31'`|
|`DATETIME`|`'1000-01-01 00:00:00.000000' to '9999-12-31 23:59:59.999999'`|
|`TIMESTAMP`|`'1970-01-01 00:00:01.000000' UTC to '2038-01-19 03:14:07.999999'`|
|`TIME`|`'-838:59:59.000000' to '838:59:59.000000'`|
|`YEAR`|`1901 to 2155`|

## 字符串类型

|类型|大小(字节)|用途|
|:--|:--|:--|
|`CHAR(M)`|`0 to 255`|固定长度的字符串,即使数据小于M长度，也会占用M长度|
|`VARCHAR(M)`|`0 to 65535`|一个可变长度的字符串，M表示在字符的最大列长度|
|`TEXT[(M)]`|`0 to 65535`|长文本列|
|`MEDIUMTEXT`|`0 to 16777215`|中等长度文本列|
|`LONGTEXT`|`4294967295 or 4GB`|极大文本列|
|`ENUM('value1','value2',...)`||枚举类型|
|`SET('value1','value2',...)`||集合类型|

- **VARCHAR(M)注**

虽然`VARCHAR(M)`使用起来较为灵活，但是从整个系统的性能角度来说，`CHAR(M)`数据类型的处理速度更快，有时甚至可以超出`VARCHAR(M)`处理速度的50%。因此，用户在设计数据库时应当综合考虑各方面的因素，以求达到最佳的平衡

- **`ENUM('value1','value2',...)`**

```sql
CREATE TABLE shirts (
    name VARCHAR(40),
    size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
);

INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','medium'),
  ('polo shirt','small');

mysql> SELECT name, size FROM shirts WHERE size = 'medium';
+---------+--------+
| name    | size   |
+---------+--------+
| t-shirt | medium |
+---------+--------+
1 row in set (0.00 sec)

UPDATE shirts SET size = 'small' WHERE size = 'large';

COMMIT;
```

- **`SET('value1','value2',...)`**

SET是一个字符串对象，它可以有`0`或`更多`个值，每个值均必须选自一个允许值列表中，该列表在表创建时被指定。

```sql
mysql> CREATE TABLE myset (col SET('a', 'b', 'c', 'd'));
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO myset (col) VALUES ('a,d'), ('d,a'), ('a,d,a'), ('a,d,d'), ('d,a,d');
Query OK, 5 rows affected (0.01 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SELECT col FROM myset;
+------+
| col  |
+------+
| a,d  |
| a,d  |
| a,d  |
| a,d  |
| a,d  |
+------+
5 rows in set (0.00 sec)
```

## 参考：

1. http://www.cnblogs.com/wupeiqi/articles/5713315.html
2. http://www.runoob.com/mysql/mysql-data-types.html
3. http://dev.mysql.com/doc/refman/5.7/en/data-type-overview.html