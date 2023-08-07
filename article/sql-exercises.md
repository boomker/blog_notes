# SQL语句练习

## 环境

数据库采用的是`postgresql`

## SQL语句练习

1. [电脑商店](/article/computer-store-sql-exercises)
2. [员工管理](/article/employee-management-sql-exercises)
3. [仓库](/article/warehouse-sql-exercises)
4. [电影院](/article/movie-theatres-sql-exercises)

## 数据库常见疑难杂症 
### 一.  数据库错误码2003
> 2003, "Can't connect to MySQL server on '127.0.0.1' ([Errno 111] ECONNREFUSED)"
* 解决办法:
	1. 检查数据库监听地址是否为 '0.0.0.0';  客户端连接参数是否包含了 'utf8';   mysql-server 配置文件是否有多余重复



