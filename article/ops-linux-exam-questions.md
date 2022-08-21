# 合格的Linux运维必会考试题及答案

## 合格的Linux运维必会考试题及答案(一)

1、如何过滤出已知当前目录下ansheng中的所有一级目录(提示:不包含ansheng目录下面目录的子目录及隐藏目录，即只能是一级目录)?

解答：

方法1：通过find直接查找指定类型的文件


```bash
[root@ansheng ~]# find ./ansheng -type d -maxdepth 1
./ansheng
./ansheng/xings
./ansheng/asgd
./ansheng/test
./ansheng/ext
./ansheng/ansheng
```

方法2：ls -l结果中以d开头的就是目录

```bash
[root@ansheng ansheng]#  ls -l|grep "^d"
[root@ansheng ~]# ls -l ./ansheng | grep "^d"
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings
```

方法3：过滤出非文件的内容

```bash
[root@ansheng ~]# ls -l ./ansheng | grep -v "^-" 
total 20
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings
```

方法4：通过给目录加标识，然后通过过滤标识，过滤出目录

```bash
[root@ansheng ~]# ls -lF ./ansheng | grep "/$"
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings/
[root@ansheng ~]# ls -lF ./ansheng | grep "/"
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings/
```

方法5：ls显示长格式，然后再通过sed过滤出以dr开头的行并打印出来

```bash
[root@ansheng ~]# ls -l ./ansheng | sed -n /^dr/p
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings
[root@ansheng ~]# ls -lF ./ansheng | sed -n '/\/$/p'  ---> 过滤出以/结尾的
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings/
```

方法6：awk过滤方法

```bash				
[root@ansheng ~]# ls -l ./ansheng | awk '/dr/'    ----->过滤出以dr开头的
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings			
[root@ansheng ~]# ls -l ./ansheng | awk '/^d/'   ----->过滤出以d开头的
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings
[root@ansheng ~]# ls -lF ./ansheng | awk '/\/$/'  ----->过滤出以/结尾的
drwxr-xr-x 2 root root 4096 Apr  9 21:08 asgd/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ext/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 ansheng/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 test/
drwxr-xr-x 2 root root 4096 Apr  9 21:08 xings/
```

2、假如当前目录是如下命令的结果:

```bash
[root@ansheng ansheng]# pwd #==>这是打印当前目录的，最菜的命令了，你该会的。
/root/ansheng
现在因为需要进入到了 /tmp 目录下进行操作，执行的命令如下：
[root@ansheng ansheng]# cd /tmp
[root@ansheng tmp]# pwd
/tmp
```

操作完毕后，希望快速返回上一次进入的目录，即/ansheng 目录，该如何做呢？（提示：不能用 cd /ansheng 命令呦）

解答：

```bash
[root@ansheng ansheng]# pwd
/root/ansheng
[root@ansheng ansheng]# cd /tmp
[root@ansheng tmp]# pwd
/tmp
[root@ansheng tmp]# cd -
/root/ansheng
[root@ansheng ansheng]# pwd
/root/ansheng
```

3、一个目录中有很多文件（ ls -l 查看时好多屏），想用一条命令最快速度查看到最近更新的文件。如何看？

解答：

```bash
[root@ansheng /]# cd /etc/
[root@ansheng etc]# touch ansheng
[root@ansheng etc]# ls -lrt
total 1668
-rw-r--r--.  1 root root   1112 Mar 30  2003 minicom.users
-rw-r--r--.  1 root root  10814 Feb 20  2006 ltrace.conf
-rw-r--r--.  1 root root    662 Aug 29  2007 logrotate.conf
-rw-r--r--.  1 root root    220 Oct 13  2008 quotagrpadmins
-rw-r--r--.  1 root root    148 May 14  2009 asound.conf
.....................................
-rw-r--r--.  2 root root     80 Jan 15 12:33 resolv.conf
-rw-r--r--.  1 root root      0 Jan 15 13:45 ansheng
[root@ansheng etc]#
```

4、在配置apache时执行了./configure --prefix=/application/apache2.2.17 来编译 apche， 在 make install 完成后，希望用户访问 apache 路径更简单，需要给/application/apache2.2.17 目录做一个软链接/application/apache，使得内部开发或管理人员通过/application/apache 就可以访问到apache 的安装目录/application/apache2.2.17 下的内容，请你给出实现的命令。（提示： apache为一个 web 服务）

解答：

```bash
[root@ansheng /]# mkdir -p /application/apache2.2.17
[root@ansheng /]# ln -s /application/apache2.2.17/ /application/apache
[root@ansheng /]# touch /application/apache2.2.17/ansheng
[root@ansheng /]# ls /application/apache
ansheng
```

 5、已知 apache 服务的访问日志按天记录在服务器本地目录/app/logs 下，由于磁盘空间紧张，现在要求只能保留最近 7 天的访问日志！请问如何解决？ 请给出解决办法或配置或处理命令。
（提示：可以从 apache 服务配置上着手，也可以从生成出来的日志上着手。) 

创建文件脚本：

```bash
#!/bin/bash
for n in `seq 14`
do
        date -s "11/0$n/14"
        touch access_www_`(date +%F)`.log
done
```

解决方法：

```bash
[root@ansheng logs]# pwd
/application/logs
[root@ansheng logs]# ll
total 0
-rw-r--r--. 1 root root 0 Jan  1 00:00 access_www_2015-01-01.log
-rw-r--r--. 1 root root 0 Jan  2 00:00 access_www_2015-01-02.log
-rw-r--r--. 1 root root 0 Jan  3 00:00 access_www_2015-01-03.log
-rw-r--r--. 1 root root 0 Jan  4 00:00 access_www_2015-01-04.log
-rw-r--r--. 1 root root 0 Jan  5 00:00 access_www_2015-01-05.log
-rw-r--r--. 1 root root 0 Jan  6 00:00 access_www_2015-01-06.log
-rw-r--r--. 1 root root 0 Jan  7 00:00 access_www_2015-01-07.log
-rw-r--r--. 1 root root 0 Jan  8 00:00 access_www_2015-01-08.log
-rw-r--r--. 1 root root 0 Jan  9 00:00 access_www_2015-01-09.log
-rw-r--r--. 1 root root 0 Jan 10 00:00 access_www_2015-01-10.log
-rw-r--r--. 1 root root 0 Jan 11 00:00 access_www_2015-01-11.log
-rw-r--r--. 1 root root 0 Jan 12 00:00 access_www_2015-01-12.log
-rw-r--r--. 1 root root 0 Jan 13 00:00 access_www_2015-01-13.log
-rw-r--r--. 1 root root 0 Jan 14 00:00 access_www_2015-01-14.log
[root@ansheng logs]# find /application/logs/ -type f -mtime +7 -name "*.log"|xargs rm –f  ##也可以使用-exec rm -f {} \;进行删除
[root@ansheng logs]# ll
total 0
-rw-r--r--. 1 root root 0 Jan  7 00:00 access_www_2015-01-07.log
-rw-r--r--. 1 root root 0 Jan  8 00:00 access_www_2015-01-08.log
-rw-r--r--. 1 root root 0 Jan  9 00:00 access_www_2015-01-09.log
-rw-r--r--. 1 root root 0 Jan 10 00:00 access_www_2015-01-10.log
-rw-r--r--. 1 root root 0 Jan 11 00:00 access_www_2015-01-11.log
-rw-r--r--. 1 root root 0 Jan 12 00:00 access_www_2015-01-12.log
-rw-r--r--. 1 root root 0 Jan 13 00:00 access_www_2015-01-13.log
-rw-r--r--. 1 root root 0 Jan 14 00:00 access_www_2015-01-14.log
```


6、调试系统服务时，希望能实时查看/var/log/messages 系统日志的更新,如何做？

解答：

```bash
[root@ansheng logs]# tail -f /var/log/messages
Jan 15 13:23:38 ansheng dhclient[869]: DHCPREQUEST on eth2 to 192.168.20.254 port 67 (xid=0x7a59bf0e)
Jan 15 13:23:38 ansheng dhclient[869]: DHCPACK from 192.168.20.254 (xid=0x7a59bf0e)
Jan 15 13:23:40 ansheng dhclient[869]: bound to 192.168.20.132 -- renewal in 776 seconds.
Jan 15 13:33:35 ansheng rz[1315]: [root] ansheng.tar.gz/ZMODEM: 425 Bytes, 22218 BPS
Jan 15 13:36:36 ansheng dhclient[869]: DHCPREQUEST on eth2 to 192.168.20.254 port 67 (xid=0x7a59bf0e)
Jan 15 13:36:36 ansheng dhclient[869]: DHCPACK from 192.168.20.254 (xid=0x7a59bf0e)
Jan 15 13:36:38 ansheng dhclient[869]: bound to 192.168.20.132 -- renewal in 686 seconds.
Jan 15 13:48:04 ansheng dhclient[869]: DHCPREQUEST on eth2 to 192.168.20.254 port 67 (xid=0x7a59bf0e)
Jan 15 13:48:04 ansheng dhclient[869]: DHCPACK from 192.168.20.254 (xid=0x7a59bf0e)
Jan 15 13:48:06 ansheng dhclient[869]: bound to 192.168.20.132 -- renewal in 790 seconds.
```

7、打印轻量级web服务的配置文件 nginx.conf内容的行号及内容，该如何做？

测试文件nginx.conf内容

```bash
[root@ansheng tmp]# cat -n nginx.conf 
     1	nginx
     2	config
     3	ansheng
     4	test
     5	centos
     6	ansheng
```

解决方法：

方法1：

```bash
[root@ansheng tmp]# cat -n nginx.conf
     1	nginx
     2	config
     3	ansheng
     4	test
     5	centos
     6	ansheng
```

方法2：

```bash
[root@ansheng tmp]# vim nginx.conf>>：>>set nu
```

方法3：

```
[root@ansheng tmp]# nl nginx.conf 
     1	nginx
     2	config
     3	ansheng
     4	test
     5	centos
     6	ansheng
```

方法4：

```bash
[root@ansheng tmp]# grep -n . nginx.conf
1:nginx
2:config
3:ansheng
4:test
5:centos
6:ansheng
```

8、装完Centos系统后，希望网络文件共享服务NFS，仅在3级别上开机自启动，该如何做

解答：

```bash
[root@ansheng /]# chkconfig --list nfs
nfs                 0:off     1:off     2:off     3:off     4:off     5:off     6:off
[root@ansheng /]# chkconfig nfs on
[root@ansheng /]# chkconfig --level 3 nfs on
[root@ansheng /]# chkconfig --list nfs
nfs                 0:off     1:off     2:on 3:on 4:on 5:on 6:off
```

9、系统运行级别一般为 0-6，请分别写出每个级别的含义。

解答：

1. 0  关机
2. 1  单用户
3. 2  多用户，没有nfs支持
4. 3  完全多用户，
5. 4  保留
6. 5  X Windows
7. 6  重启

10、ansheng 系统中查看中文乱码，请问如何解决乱码问题？

解答：

查看当前语言

```bash
echo $LANG
```

查看已安装语言包，如果没有中文语言包就安装中文语言包

```bash
locale -a|grep zh
```

修改当前字符集

```bash
LANG="zh_CN.gb18030"
```

让其生效

```bash
source /etc/sysconfig/i18n
```

修改字符集

```bash
vi /etc/sysconfig/i18n
```

让其永久生效

```bash
source /etc/sysconfig/i18n
```

11、如何优化 ansheng 系统（可以不说太具体）？

解答：

1. 不用root，添加普通用户，通过sudo授权管理
2. 更改默认的远程连接SSH服务端口及禁止root用户远程连接
3. 定时自动更新服务器时间
4. 配置国内yum源
5. 关闭seansheng及iptables（iptables工作场景如果有外网IP一定要打开，高并发除外）
6. 调整文件描述符的数量
7. 精简开机启动服务（crond rsyslog  network  sshd）
8. ansheng内核参数优化（/etc/sysctl.conf）
9. 更改字符集，支持中文，但建议还是用英文字符集，防止乱码
10. 锁定关键系统文件
11. 清空/etc/issue，去除系统及内核版本登录前的屏幕显示

12、/etc/目录为 ansheng 系统的默认的配置文件及服务启动命令的目录

*a.请用 tar 打包/etc 整个目录（打包及压缩）*

*b.请用 tar 打包/etc 整个目录（打包及压缩，但需要排除/etc/services 文件）*

*c.请把 a 点命令的压缩包，解压到/tmp 指定目录下（最好只用 tar 命令实现）*

解答：

a.

打包：

```bash
[root@ansheng tmp]# tar zcfP  etc.tar /etc
[root@ansheng tmp]# ll
total 9072
-rw-r--r--. 1 root root 9289256 Jan 14 00:21 etc.tar
```

压缩：

```bash
[root@ansheng tmp]# tar zxvf etc.tar
[root@ansheng tmp]# ll
total 9076
drwxr-xr-x. 83 root root    4096 Jan 14 00:15 etc
-rw-r--r--.  1 root root 9289256 Jan 14 00:21 etc.tar
```

b.

打包：

```bash
[root@ansheng /]# tar zcvf etc.tar.gz /etc --exclude=/etc/services
[root@ansheng /]# ll etc.tar.gz 
-rw-r--r--. 1 root root 9161575 Jan 14 00:25 etc.tar.gz
[root@ansheng /]# tar zxvf etc.tar
```

c.

```bash
[root@ansheng /]# tar zxvf etc.tar.gz -C /opt/
[root@ansheng /]# ll /opt/
drwxr-xr-x. 83 root root     4096 Jan 14 00:15 etc
```

13.已知如下命令及结果：

```
[ansheng@test ~]$ echo "I am ansheng,myqq is 31333741">>ansheng.txt
[ansheng@test ~]$ cat ansheng.txt
I am ansheng, myqq is 31333741
```

现在需要从文件中过滤出“ ansheng” 和“ 31333741” 字符串，请给出命令.

解答：

没有逗号：I am ansheng  myqq is 31333741

```bash
[root@ansheng tmp]# cat ansheng 
I am ansheng  myqq is 31333741
```

方法1：

```bash
[ansheng@ansheng ~]$ awk '{print $3" "$6}' ansheng
ansheng 31333741
```

方法2：

```bash
[ansheng@ansheng ~]$ cut -d" " -f3,6 ansheng 
ansheng 31333741
```

方法3：

```bash
[ansheng@ansheng ~]$ cut -c 6-11,20- ansheng
ansheng 31333741
```

有逗号：I am ansheng, myqq is 31333741

```bash
[root@ansheng tmp]# cat ansheng 
I am ansheng, myqq is 31333741
```

方法1：

```bash
[ansheng@ansheng ~]$ cut -c 6-11,20- ansheng
anshengs 31333741
```

方法2：

```bash
[ansheng@ansheng ~]$ cut -d" " -f3,6 ansheng|sed s#,#" "#g
anshengs 31333741
```

方法3：

```bash
[ansheng@ansheng ~]$ awk '{print $3" "$6}' ansheng|sed s/,/" "/g
anshengs 31333741
```

方法4：

```bash
[ansheng@ansheng ~]$ awk -F '[ ,]' '{print $3" "$6}' ansheng
anshengs 31333741
```

方法5：

```bash
[ansheng@ansheng ~]$ cut -d" " -f3,5 ansheng |tr ",myqq" " "
anshengs 31333741
```

14、如何查看/etc/services 文件内容有多少行？

解答：

方法1：

```bash
[root@ansheng /]# wc -l /etc/services
10774 /etc/services
```

方法2：

```bash
[root@ansheng /]# cat -n /etc/services |tail -1
 10774	iqobject        48619/udp               # iqobject
```

方法3：

```bash
[root@ansheng /]# sed -n '$=' /etc/services
10774
```

方法4：

```bash
[root@ansheng /]# awk '{print NR}' /etc/services|tail -1
10774
```

方法5：

```bash
[root@ansheng /]# grep -n $ /etc/services|tail -1
10774:iqobject        48619/udp               # iqobject
```

15、过滤出/etc/services 文件包含 3306 或 1521 两数据库端口的行的内容。

解答：

```bash
[root@ansheng /]# grep -E "3306|1521" /etc/services
mysql           3306/tcp                        # MySQL
mysql           3306/udp                        # MySQL
ncube-lm        1521/tcp                # nCube License Manager
ncube-lm        1521/udp                # nCube License Manager
```


## 合格的Linux运维必会考试题及答案(二)

1、描述 linux 系统从开机到登陆界面的启动过程

开机自检，MBR引导，读取硬盘0柱面0磁道1扇区的前446字节，加载grub菜单，在grub菜单里面加载kernel，启动init进程，init是Linux系统启动时第一个启动的进程，init读取inittab文件，先执行/etc/rc.d/rc.sysinit初始化脚本（设置主机名，加载inittab，设置网卡和一些PCI设备），根据inittab设置的级别指向相对应的脚本，如果是3模式则指向/etc/rc3.d下面的脚本以及程序，执行rc.local，最后启动mingetty进程，进入登陆界面。

2、描述 linux 下软链接和硬链接的区别

在linux系统中，链接分两种 ：一种是硬链接（Hard Link），另一种被称为符号链接或软链接（Symbolic Link）。

1. 默认不带参数情况下，ln命令创建的是硬链接。
2. 硬链接文件与源文件的inode节点号相同，而软链接文件的inode节点号与源文件不同。
3. ln命令不能对目录创建硬链接，但可以创建软链接，对目录的软链接会经常被用到。
4. 删除软链接文件,对源文件及硬链接文件无任何影响；
5. 删除文件的硬链接文件，对源文件及软链接文件无任何影响；
6. 删除链接文件的原文件，对硬链接文件无影响，会导致其软链接失效（红底白字闪烁状）；
7. 同时删除原文件及其硬链接文件，整个文件才会被真正的删除。
8. 很多硬件设备中的快照功能，使用的就类似硬链接的原理。
9. 软连接可以跨文件系统，硬链接不可以跨文件系统。

3、描述 linux shell 中单引号、双引号及不加引号的简单区别

*单引号：*

即将单引号内的内容原样输出，或者描述为单引号里面看到的是什么就会输出什么。

*双引号：*

把双引号内的内容输出出来；如果内容中有命令、变量等，会先把变量、命令解析出结果，然后在输出最终内容来。

*不加引号：*

不会将含有空格的字符串视为一个整体输出, 如果内容中有命令、变量等，会先把变量、命令解析出结果，然后在输出最终内容来，如果字符串中带有空格等特殊字符，则不能完整的输出，需要改加双引号，一般连续的字符串，数字，路径等可以用。

4、描述 linux 运行级别 0-6 的各自含义

1. 0 关机
2. 1 单用户模式
3. 2 多用户没有NFS网络支持
4. 3 完全多用户模式（工作中常用）
5. 4 保留
6. 5 图形化界面
7. 6 重启

5、描述 linux 下文件删除的原理

当一个文件的I_link=0且没有进程占用的时候，这个文件就被删除了，还有一种情况就是被进程占用的情况下，当这个文件的I\_link和I_count同时为0的时候这个文件才会被真正的删除。


6、如何取得/ansheng 文件的权限对应的数字内容，如-rw-r--r-- 为 644， 要求使用命令取得644 这样的数字。

解答：

方法1：

```bash
[root@ansheng ~]# stat /ansheng 
  File: `/ansheng'
  Size: 0           Blocks: 0          IO Block: 4096   regular empty file
Device: 803h/2051d  Inode: 16          Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2015-01-11 05:42:44.762128948 -0500
Modify: 2015-01-11 05:42:44.762128948 -0500
Change: 2015-01-11 05:42:44.762128948 -0500
[root@ansheng ~]# stat /ansheng |sed -nr 's#^.*s: ((.*)/-r.*#\1#gp'
0644
```

awk方法：

```bash
[root@ansheng ~]# stat /ansheng |awk -F "[(/]" 'NR==4{print $2}'
0644
```

State方法：

```bash
[root@ansheng ~]# stat -c %a /ansheng 
644
```


```bash
[root@ansheng ansheng]# ll /ansheng |cut -c2-10|tr rwx- 4210|awk -F "" '{print $1+$2+$3 $4+$5+$6 $7+$8+$9}'
644
```

7、linux下通过 mkdir 命令创建一个新目录/ansheng/ett，它的硬链接数是多少，为什么？

解答：

```bash
[root@ansheng ~]# mkdir /ansheng/ett -p
[root@ansheng ~]# ll -d /ansheng/ett/
drwxr-xr-x. 2 root root 4096 Jan 11 06:02 /ansheng/ett/
```

它的链接数是2，本身算一个链接，ett目录下的.为ett目录的又一个链接，所以链接数为2

```bash
[root@ansheng ansheng]# ll -i
total 4
1046536 drwxr-xr-x. 2 root root 4096 Jan 11 06:02 ett
[root@ansheng ansheng]# ll -di ett/.
1046536 drwxr-xr-x. 2 root root 4096 Jan 11 06:02 ett/.
```

8、请执行命令取出 linux 中 eth0 的 IP 地址(请用 cut，有能力者也可分别用 awk,sed 命令答)。

解答：

cut方法1：

```bash
[root@ansheng ~]# ifconfig eth0|sed -n '2p'|cut -d ":" -f2|cut -d " " -f1
192.168.20.130
```

awk方法2：

```bash
[root@ansheng ~]# ifconfig eth0|awk 'NR==2'|awk -F ":" '{print $2}'|awk '{print $1}'
192.168.20.130
```

Awk多分隔符方法3：

```bash
[root@ansheng ~]# ifconfig eth0|awk 'NR==2'|awk -F "[: ]+" '{print $4}'
192.168.20.130
```

Sed方法4：

```bash
[root@ansheng ~]# ifconfig eth0|sed -n '/inet addr/p'|sed -r 's#^.*ddr:(.*)Bc.*$#\1#g'
192.168.20.130
[root@ansheng ~]# ifconfig eth0|sed -n '/inet addr/p'|sed 's#^.*ddr:##g'|sed 's#B.*$##g'
192.168.20.130
```


9、请给出默认情况 eth0 网卡配置文件的路径及客户端 DNS 的路径。

解答：

Eth0网卡配置路径：/etc/sysconfig/network-scripts/ifcfg-eth0
客户端DNS配置路径：/etc/resolv.conf

10、查找当前目录下所有文件，并把文件中的 www.ansheng.me 字符串替换成 www.abc.cc

解答：

```bash
[root@ansheng ~]# find ./ -type f|xargs sed -i 's#www\.ansheng\.me#www\.abc\.cc#g'
```

11、问题：如何赋予 ansheng 文件 -rw-r--r-x 权限属性

解答：

```bash
[root@ansheng ~]# touch ansheng
[root@ansheng ~]# ll
total 0
-rw-r--r--. 1 root root 0 Jan 11 06:33 ansheng
[root@ansheng ~]# chmod 645 ansheng 
[root@ansheng ~]# ll
total 0
-rw-r--r-x. 1 root root 0 Jan 11 06:33 ansheng
```

12、执行下面命令时发现提示需要输入密码,请问提示输入的密码是哪个用户的密码。

```bash
[test@ansheng ~]$ sudo su - ansheng
```

解答：输入的是执行sudo命令的用户密码。

相关说明：

| 实际命令 | 命令说明 |
| :-------- | :----- |
| su – / su – root | 该命令是真正用户切换命令（默认是切换到root），输入的是root的密码 |
| sudo su – | 该命令是通过sudo权限进行角色转换（默认是切换到root），输入的是指向命令当时账号的密码，而非root密码 |
| sudo su - ansheng | 该命令实际意思是通过sudo,以root的权限，进行su – ansheng用户切换，因此需要输入的是指向命令当时账号的密码，和sudo ls /root是一样的 |

13、请问在一个命令上加什么参数可以实现下面命令的内容在同一行输出。

```bash
echo "ansheng";echo "ansheng"
```

解答：

```bash
[root@ansheng ~]# echo -n "ansheng";echo "ansheng"
anshengansheng
```

14、请给出如下格式的 date 命令 例： 11-02-26。在给出实现按周输出 比如：周六输出为 6，请分别给出命令。

解答：

```bash
[root@ansheng ~]# date +%y-%m-%d
15-01-11
```

15、当从 root 用户切到普通用户时，执行 ifconfig 会提示。

```bash
[a@student ~]$ ifconfig
-bash: ifconfig: command not found
```

提示： c58 会遇到， c64 没有此问题。

请问这是为什么？如何解决，请给出详细解决过程。

解答：没有找到这个命令，ifconfig命令所在目录没有添加到PATH环境变量里面，解决步骤：

```bash
[root@ansheng ~]# /sbin/ifconfig
```

或者写入PATH环境变量

16、扩展问题：打印三天前的日期格式如： 2011-02-26

解答：

```bash
[root@ansheng ~]# date +%F
2015-01-11
[root@ansheng ~]# date +%F -d '3 day ago'
2015-01-08
[root@ansheng ~]# date +%F -d '-3 day'
2015-01-08
```

17、已知/ansheng/test.txt 文件内容为：

```bash
ansheng

xizi

xiaochao
```

请问如何把文件中的空格过滤掉（要求命令行实现）。

解答：

grep方法1：

```bash
[root@ansheng ~]# grep -v "^$" test.txt 
ansheng
xizi
xiaochao
```

sed方法2：

```bash
[root@ansheng ~]# sed '/^$/d' test.txt 
ansheng
xizi
xiaochao
```

awk方法3：

```bash
[root@ansheng ~]# awk /^[^$]/ test.txt 
ansheng
xizi
xiaochao
```

18、已知/ansheng/ett.txt 文件内容为：

<pre>
ansheng
anshengme
test
</pre>

请使用 grep 或 egrep 正则匹配的方式过滤出前两行内容

解答：

方法1：

```bash
[root@ansheng ansheng]# grep "ol" ett.txt 
ansheng
anshengme
```

方法2：

```bash
[root@ansheng ansheng]# grep "oy" ett.txt 
ansheng
anshengme
```

方法3：

```bash
[root@ansheng ansheng]# grep "ol.*oy" ett.txt 
ansheng
anshengme
```

方法4：

```bash
[root@ansheng ansheng]# egrep "ol.+oy" ett.txt 
ansheng
anshengme
```

19、如何快速查到 ifconfig 的全路径（假如你不知道其路径）， 请给出命令。

解答：

whereis方法1：

```bash
[root@ansheng ~]# whereis -b ifconfig
ifconfig: /sbin/ifconfig
```

which方法2：

```bash
[root@ansheng ~]# which ifconfig
/sbin/ifconfig
```

20、请给出查看当前哪些用户在线的 linux 命令。

解答：

```bash
[root@ansheng /]# w
 07:51:32 up  3:04,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    192.168.20.1     04:48    0.00s  0.65s  0.00s w
```

```bash
[root@ansheng /]# who
root     pts/0        2015-01-11 04:48 (192.168.20.1)
```

21、请给出正确的关机和重起服务器的命令。

解答：

关机：

```bash
[root@ansheng /]# shutdown -h now   --->立刻关机（生产常用）
[root@ansheng /]# shutdown -h +1    --->1分钟后关机
[root@ansheng /]# init 0
[root@ansheng /]# halt          --->立即停止系统，需要人工关闭i安源
[root@ansheng /]# halt -p
[root@ansheng /]# poweroff      --->立即停止系统，并且关闭电源
```

重启：

```bash
[root@ansheng /]# reboot                --->生产常用
[root@ansheng /]# shutdown -r now       --->生产常用
[root@ansheng /]# shutdown -r +1        --->1分钟后重启
[root@ansheng /]# init 6
```

注销：

```bash
[root@ansheng /]# logout
[root@ansheng /]# exit              --->生产常用
[root@ansheng /]# Ctrl+d            --->快捷键，生产常用
```

22、请写出下面 linux SecureCRT 命令行快捷键命令的功能？

1. Ctrl + a 
2. Ctrl + c 
3. Ctrl + d 
4. Ctrl + e 
5. Ctrl + l 
6. Ctrl + u 
7. Ctrl + k
8. tab
9. Ctrl+shift+c
10. Ctrl+shift+v

解答：

1. Ctrl + a          ---->光标移动到行首
2. Ctrl + e          ---->光标移动到行尾
3. Ctrl + c          ---->终止当前程序
4. Ctrl + d          ---->如果光标前有字符则删除，没有则退出当前中断
5. Ctrl + l          ---->清屏
6. Ctrl + u          ---->剪切光标以前的字符
7. Ctrl + k          ---->剪切光标以后的字符
8. Ctrl + y          ---->复制u/k的内容
9. Ctrl + r          ---->查找最近用过的命令
10. tab               ---->命令或路径补全
11. Ctrl+shift+c      ---->复制
12. Ctrl+shift+v      ---->粘贴

## 合格的Linux运维必会考试题及答案(三)

1.每隔1分钟，打印一个+号到 ansheng.log ,请给出 crontab 完整命令。

解答：

```bash
[root@ansheng ~]# crontab -e
*/1 * * * * /bin/echo "+" >> /root/ansheng.log
[root@ansheng ~]# /etc/init.d/crond restart
Stopping crond:                                            [  OK  ]
Starting crond:                                            [  OK  ]
```

2.每隔 2 个小时将/etc/services 文件打包备份到/tmp 下（最好每次备份成不同的备份包）。

解答：

```bash
[root@ansheng ~]# crontab -e
00 */2 * * * /bin/tar zcvf /tmp/services-`date +%F-%H`tar.gz /etc/services
[root@ansheng ~]# /etc/init.d/crond restart
Stopping crond:                                            [  OK  ]
Starting crond:                                            [  OK  ]
```

3.每天晚上 12 点，打包站点目录/var/www/html 备份到/data 目录下（最好每次备份按时间生成不同的备份包）

解答：

```bash
[root@ansheng ~]# cat a.sh 
#/bin/bash
cd /var/www/ && /bin/tar zcf /data/html-`date +%m-%d-%H`.tar.gz html/
[root@ansheng ~]# crontab –e
00 00 * * * /bin/sh /root/a.sh
```

4.每周 六、日 上午 9:00 和下午 14： 00 来老男孩这里学习 (执行程序/server/script/ansheng.sh代替学习 )。

解答：

```bash
[root@ansheng /]# crontab -e
00 09,14 * * 6,7 /bin/sh /server/script/ansheng.sh
```

5.请描述下列路径的内容是做什么的？

1. /etc/sysctl.conf
2. /etc/rc.local
3. /etc/hosts
4. /etc/fstab
5. /var/log/secure

解答：

1. /etc/sysctl.conf          ----->内核调优的文件
2. /etc/rc.local             ----->开机自启动命令的文件
3. /etc/hosts                ----->本机的域名解析文件
4. /etc/fstab                ----->开机设备自动挂载的文件
5. /var/log/secure           ----->系统登陆的安全日志

6.请说出下列 grep 正则表达式的含义

1. ^
2. $
3. .(点号)
4. \
5. *
6. \{n,m\}
7. [^t]
8. ^[^t]

解答：

1. ^             ----->以什么什么开头的
2. $             ----->以什么什么结尾的
3. .(点号)     ----->代表任意一个字符
4. \             ----->转义符，让有着特殊意义的字符变成没有任何意义的字符
5. *             ----->重复前面的字符0次或多次
6. \{n,m\}       ----->前面的字符重复N到M次
7. [^t]          ----->过滤出不包含t的
8. ^[^t]         ----->过滤出不以t开头的

8.授权 ansheng 目录及其子目录 755 的权限，请给出命令。

解答：

```bash
[root@ansheng /]# ll -d ansheng/
drwxr-xr-x. 3 root root 4096 Jan 14 04:57 ansheng/
[root@ansheng /]# ll ansheng/
total 4
drwxr-xr-x. 2 root root 4096 Jan 14 04:57 linux
-rw-r--r--. 1 root root    0 Jan 14 04:57 test
[root@ansheng /]# chmod -R 755 /ansheng/
[root@ansheng /]# ll ansheng/
total 4
drwxr-xr-x. 2 root root 4096 Jan 14 04:57 linux
-rwxr-xr-x. 1 root root    0 Jan 14 04:57 test
[root@ansheng /]# ll -d ansheng/
drwxr-xr-x. 3 root root 4096 Jan 14 04:57 ansheng/
```

9.把 ansheng 目录及其子目录的属主改为 ansheng,组改为 root,请给出命令。

解答：

```bash
[root@ansheng /]# ll ansheng/
total 4
drwxr-xr-x. 2 root root 4096 Jan 14 04:57 linux
-rwxr-xr-x. 1 root root    0 Jan 14 04:57 test
[root@ansheng /]# ll -d ansheng/
drwxr-xr-x. 3 root root 4096 Jan 14 04:57 ansheng/
[root@ansheng /]# chown -R ansheng:root ansheng/
[root@ansheng /]# ll ansheng/
total 4
drwxr-xr-x. 2 ansheng root 4096 Jan 14 04:57 linux
-rwxr-xr-x. 1 ansheng root    0 Jan 14 04:57 test
[root@ansheng /]# ll -d ansheng/
drwxr-xr-x. 3 ansheng root 4096 Jan 14 04:57 ansheng/
```

10.描述下 umask 的作用，并举例。

解答：

Umask是设置Linux创建文件或者创建目录默认权限的，默认的权限文件是644，目录是755，目录计算方法：默认最大权限 - umask权限 = 创建目录默认权限文件计算方法：偶数：默认最大权限-umask权限=创建文件默认权限；奇数：默认最大权限-umask权限+（Umask奇数对应权限位+1）=创建文件默认权限

11.添加一个用户 ansheng，并指定属于 sa 组，要求组 ID 为 801， uid 为 808,并且不建立家目录及禁止其登陆。

解答：

```bash
[root@ansheng /]# groupadd sa -g 801
[root@ansheng /]# useradd ansheng -g sa -s /sbin/nologin -M
[root@ansheng /]# id ansheng
uid=500(ansheng) gid=801(sa) groups=801(sa)
[root@ansheng /]# su - ansheng
su: warning: cannot change directory to /home/ansheng: No such file or directory
This account is currently not available.
[root@ansheng /]# ls /home/
```

12.如何查看用户的 uid 及属于的组信息。

解答：

```bash
[root@ansheng /]# id ansheng
uid=500(ansheng) gid=801(sa) groups=801(sa)
```