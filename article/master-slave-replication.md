#  MySQL配置主从复制

## 环境说明

|主机名|IP|说明|
|:--|:--|:--|
|node01|172.16.10.10|MySQL主服务器|
|node02|172.16.10.11|MySQL从服务器|

## 主从同步的详细过程

1. 主服务器验证连接。
2. 主服务器为从服务器开启一个线程。
3. 从服务器将主服务器日志的偏移位告诉主服务器。
4. 主服务器检查该值是否小于当前二进制日志偏移位。
5. 如果小于，则通知从服务器来取数据。
6. 从服务器持续从主服务器取数据，直至取完，这时，从服务器线程进入睡眠，主服务器线程同时进入睡眠。
7. 当主服务器有更新时，主服务器线程被激活，并将二进制日志推送给从服务器，并通知从服务器线程进入工作状态。
8. 从服务器SQL线程执行二进制日志，随后进入睡眠状态。

## 实战配置MySQL主从复制

### 主从服务器分别作以下操作

1. 版本一致
2. 初始化表，并在后台启动mysql
3. 修改root的密码

### 修改主服务器master

```bash
[root@node01 ~]# egrep "log-bin|server-id" /etc/my.cnf 
server-id       = 4  						#服务器唯一ID，默认是1，一般取IP最后一段 
log-bin=/application/mysql/data/mysql-bin 	#启用二进制日志
```

#### 重启MySQL

```bash
[root@node01 ~]# /etc/init.d/mysqld restart
Shutting down MySQL. SUCCESS! 
Starting MySQL.. SUCCESS! 
```

## 修改从服务器slave

```bash
[root@node02 ~]# egrep "log-bin|server-id" /etc/my.cnf 
server-id       = 11						#服务器唯一ID，默认是1，一般取IP最后一段
log-bin=/application/mysql/data/mysql-bin	#启用二进制日志
```

### 重启MySQL

```bash
[root@node02 ~]# /etc/init.d/mysqld restart
Shutting down MySQL. SUCCESS! 
Starting MySQL.. SUCCESS! 
```

## 在主服务器上建立帐户并授权slave

```bash
[root@node01 ~]# mysql -uroot -pansheng.me
mysql> grant replication slave on *.* to 'rep'@'172.16.10.%' identified by '123456';
mysql> flush privileges;
```

一般不用root帐号,"%"表示所有客户端都可能连，只要帐号，密码正确，此处可用具体客户端IP代替，如192.168.145.226，加强安全。

## 在主服务器上进行数据的全备

为了保持数据的一致性，先把主服务锁定，然后进行全备，最后恢复到从服务器上

```bash
mysql> flush table with read lock; 			#锁定写入操作
[root@node01 ~]# mysqldump -uroot -pansheng.me --events -B -A --master-data=2 | gzip >/opt/rep_bak.sql.gz 	#主服务器上的所有数据进行全备操作
[root@node01 ~]# scp /opt/rep_bak.sql.gz root@172.16.10.11:/tmp/		#把备份的数据传输到从服务器上面
[root@node01 ~]# mysql -uroot -pansheng.me
mysql> show master status;			#查看位置
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000002 |      333 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

mysql> unlock tables;	#解锁
Query OK, 0 rows affected (0.00 sec)

```

## 在从服务器上面恢复主的全备

```bash
[root@node02 ~]# gzip -d /tmp/rep_bak.sql.gz 
[root@node02 ~]# mysql -uroot -pansheng.me < /tmp/rep_bak.sql 
```

## 配置从服务器

```bash
[root@node02 ~]# mysql -uroot -pansheng.me
mysql> change master to master_host='172.16.10.10',master_user='rep',master_password='123456',master_port=3306;
#设置服服务器的用户信息
mysql> change master to master_log_file='mysql-bin.000002',master_log_pos=333;
#设置binlog信息
```

## 查看信息

```bash
mysql> start slave;
#启动slave进程
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 172.16.10.10
                  Master_User: rep
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000002
          Read_Master_Log_Pos: 333
               Relay_Log_File: node02-relay-bin.000002
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000002
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 333
              Relay_Log_Space: 410
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 4
1 row in set (0.00 sec)
```

现在可以在我们的主服务器做一些更新的操作,然后在从服务器查看是否已经更新