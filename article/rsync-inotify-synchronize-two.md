# rsync+inotify实现文件实时同步（二）

## 环境说明

|主机名|IP|说明|
|:--|:--|:--|
|server|172.16.10.10|服务端|
|client|172.16.10.100|客户端|

```bash
[root@server ~]# uname -a
Linux server 2.6.32-573.22.1.el6.x86_64 #1 SMP Wed Mar 23 03:35:39 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
[root@server ~]# cat /etc/redhat-release 
CentOS release 6.7 (Final)
```

## 配置rsync服务

rsync服务端配置在`172.16.10.10`服务器上面。

### 安装rsync服务

```bash
[root@server ~]# yum -y install rsync
```

### 添加rsync服务的用户

```bash
[root@server ~]# useradd -M -s /sbin/nologin rsync
[root@server ~]# id rsync
uid=501(rsync) gid=501(rsync) groups=501(rsync)
```

### 创建/etc/rsyncd.conf配置文件

```bash
[root@server ~]# vim /etc/rsyncd.conf
#rsync_config_______________start
uid = rsync                             #指定用户
gid = rsync                             #指定组
use chroot = no
max connections = 200                   #最大链接数
timeout = 300                           #超时时间
pid file = /var/run/rsyncd.pid          #PID存放路径
lock file = /var/run/rsync.lock         #锁文件
log file = /var/log/rsyncd.log          #日志文件存放路径
[ansheng]                               #模块，链接的标签
path = /ansheng/                        #共享的路径
ignore errors                           #忽略错误
read only = false                       #只读为假，可写
list = false                            #不允许远程列表，不能看文件内容
hosts allow = 172.16.10.0/24            #允许
hosts deny = 0.0.0.0/32                 #拒绝
auth users = rsync_backup               #虚拟用户
secrets file = /etc/rsync.password      #密码文件存放路径 user:password
#rsync_config_______________end
```

### 生成密码文件

```bash
[root@server ~]# echo "rsync_backup:ansheng" > /etc/rsync.password
[root@server ~]# cat /etc/rsync.password
rsync_backup:ansheng
```

### 为密码文件设置权限

```bash
[root@server ~]# chmod 600 /etc/rsync.password
```

### 创建共享的目录并授权rsync管理

```bash
[root@server ~]# mkdir /ansheng
[root@server ~]# chown -R rsync.rsync /ansheng/
[root@server ~]# ll -d /ansheng/
drwxr-xr-x 2 rsync rsync 4096 Apr 26 14:19 /ansheng/
```

### 启动rsync服务并检查

```bash
[root@server ~]# rsync --daemon
[root@server ~]# ps -ef | grep "rsync" | grep -v "grep"
root       1155      1  0 14:20 ?        00:00:00 rsync --daemon
[root@server ~]# netstat -tulnp | grep 873
tcp        0      0 0.0.0.0:873                 0.0.0.0:*                   LISTEN      1155/rsync          
tcp        0      0 :::873                      :::*                        LISTEN      1155/rsync
[root@server ~]# lsof -i :873
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
rsync   1155 root    4u  IPv4  10943      0t0  TCP *:rsync (LISTEN)
rsync   1155 root    5u  IPv6  10944      0t0  TCP *:rsync (LISTEN)
```

### 加入开机自启动

```bash
[root@server ~]# echo "/usr/bin/rsync --daemon" >>/etc/rc.local
[root@server ~]# tail -1 /etc/rc.local 
/usr/bin/rsync --daemon
```

## 配置rsync客户端

客户端`172.16.10.100`配置rsync客户端实现推送功能。

### 生成链接服务器需要的密码文件

```bash
[root@client ~]# echo "ansheng" > /etc/rsync.password
```

### 设置密码文件权限

```bash
[root@client ~]# chmod 600 /etc/rsync.password 
```

### 文件同步测试

#### 拉取

```bash
[root@client ~]# rsync -avz rsync_backup@172.16.10.10::ansheng /rsync_client/ --password-file=/etc/rsync.password 
```

#### 推送

```bash
[root@client ~]# rsync -avz /tmp/ rsync://rsync_backup@172.16.10.10/ansheng/ --password-file=/etc/rsync.password
```

#### 推送的时候排单个或多个文件

排除单个文件

```bash
[root@client ~]# rsync -avz --exclude=rsync_test.txt /tmp/ rsync_backup@172.16.10.10::ansheng --password-file=/etc/rsync.password
```

排除多个文件

```bash
[root@client ~]# rsync -avz --exclude={rsync_exclu.txt,rsync_test.txt} /tmp/ rsync_backup@172.16.10.10::ansheng  --password-file=/etc/rsync.password
```

#### 完全同步

```bash
[root@client ~]# rsync -avz --delete /rsync_client/ rsync_backup@172.16.10.10::ansheng --password-file=/etc/rsync.password
```

完全同步会把服务端和本地端的文件保持一致

#### 通道模式

一般配合ssh key使用

```bash
[root@client ~]# rsync -avzP -e 'ssh -p 22' /etc root@172.16.10.10:/tmp
```

## rsync客户端配置inotify

inotify监控rsync客户端上面的文件，一旦有变化就上传到rsync服务端。

### 查看当前系统是否支持inotify

在开始安装inotify-tools前请先确认你的Linux内核是否啊到了2.6.13，并且在编译时开启CONFIG_INOTIFY选项，也可以通过以下命令检测。


```bash
[root@client ~]# uname -r
2.6.32-573.22.1.el6.x86_64
[root@client ~]# ls -l /proc/sys/fs/inotify/
total 0
-rw-r--r-- 1 root root 0 Apr 26 14:42 max_queued_events
-rw-r--r-- 1 root root 0 Apr 26 14:42 max_user_instances
-rw-r--r-- 1 root root 0 Apr 26 14:42 max_user_watches
```

### 下载inotify源码包

```bash
[root@client ~]# cd /usr/local/src/
[root@client src]# wget http://github.com/downloads/rvoicilas/inotify-tools/inotify-tools-3.14.tar.gz
```

### 编译安装inotify

```bash
[root@client src]# tar xf inotify-tools-3.14.tar.gz 
[root@client src]# cd inotify-tools-3.14
[root@client inotify-tools-3.14]# ./configure --prefix=/usr/local/inotify-tools-3.14
[root@client inotify-tools-3.14]# make && make install
[root@client inotify-tools-3.14]# cd ../
[root@client src]# ln -s /usr/local/inotify-tools-3.14 /usr/local/inotify
```

```bash
[root@client src]# cd /usr/local/inotify
[root@client inotify]# ll
total 16
drwxr-xr-x 2 root root 4096 Apr 26 14:45 bin			#inotify执行命令（二进制）
drwxr-xr-x 3 root root 4096 Apr 26 14:45 include		#inotify程序所需用的头文件
drwxr-xr-x 2 root root 4096 Apr 26 14:45 lib			#动态链接的库文件
drwxr-xr-x 4 root root 4096 Apr 26 14:45 share			#帮助文档
```

### 脚本自动监控

当在`/rsync_client`目录下创建文件的时候会自动推送到rsync服务端

```bash
[root@client ~]# cat inotify.sh 
#!/bin/bash
/usr/local/inotify/bin/inotifywait -mrq  --format '%w%f' -e create,close_write,delete /rsync_client \
|while read file
  do
    cd //rsync_client &&\
    rsync -az ./ --delete rsync_backup@172.16.10.10::ansheng --password-file=/etc/rsync.password
  don
```