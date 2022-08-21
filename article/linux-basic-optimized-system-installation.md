# CentOS6.x系统安装后的基本优化与安全设置

### 一. 关闭SELinux功能

SELinux(Security-Enhanced Linux) 是美国国家安全局（NSA）对于强制访问控制的实现，是 Linux历史上最杰出的新安全子系统。

*** SELinux有三种状态： ***

1. enforcing - SELinux security policy is enforced.
2. permissive - SELinux prints warnings instead of enforcing.
3. disabled - No SELinux policy is loaded.

它的配置文件在：/etc/selinux/config和/etc/sysconfig/selinux，只不过/etc/sysconfig/selinux是连接到/etc/selinux/config的
```bash
[root@AnSheng ~]# ls -l /etc/sysconfig/selinux
lrwxrwxrwx. 1 root root 17 Nov 17 16:28 /etc/sysconfig/selinux ../selinux/config
```
用sed命令修改配置文件，使其永久关闭

方法1：
```bash
[root@AnSheng ~]# grep "^SELINUX=" /etc/selinux/config
SELINUX=enforcing
[root@AnSheng ~]# sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config
[root@AnSheng ~]# grep "^SELINUX=" /etc/selinux/config
SELINUX=disabled
```

方法2：
```bash
[root@AnSheng ~]# grep "^SELINUX=" /etc/selinux/config
SELINUX=enforcing
[root@AnSheng ~]# sed -i "s#$(awk -F "[= ]+" 'NR==7{print $2}' /etc/selinux/config)#$(awk 'NR==6{print $2}' /etc/selinux/config)#g" /etc/selinux/config
[root@AnSheng ~]# grep "^SELINUX=" /etc/selinux/config
SELINUX=disabled
```

查看及修改SELinux当前状态
```bash
[root@AnSheng ~]# getenforce
Enforcing
[root@AnSheng ~]# setenforce ---命令直接回车可以看见帮助
usage: setenforce [ Enforcing | Permissive | 1 | 0 ]
[root@AnSheng ~]# setenforce 0
[root@AnSheng ~]# getenforce
Permissive
```
生产场景Linux服务器不能随便重启，若要不重启关闭selinux，首先修改selinux配置问津改为disabled，然后再用setenforce修改当前状态，这样不重启也可以关闭selinux了。

### 二. 精简系统开机启动项

和Windows系统一样，在Linux服务器运行的过程中，也会有很多无用的软件服务默认就在运行，这些服务占用了很多系统资源，而且也带来了安全隐患，因此要关闭掉。

建议企业环境新装Linux系统之后必须自动开启的服务：

1. sshd 远程连接Linux服务器，，需要这个服务程序，必须开启，否则无法远程连接Linux服务器（必须开启）
2. rsyslog 是操作系统的一种机制，守护程序通常使用这些机制将各种信息写到各个系统日志文件（必须开启）
3. network 激活/关闭启动时的各个网口（必须）应考虑开启
4. crond 周期的运行用户调度的任务，配置更简单，有周期执行任务时，要开启

将来根据服务器的业务使用场景可以进行调整，所以调整就是增加，上面的四个服务中只能增加不能减少，关闭系统所有开机自启动服务的方法：

方法1：
```bash
[root@AnSheng ~]# chkconfig --list|grep "3:on"|awk '{print $1}'|sed 's#^#chkconfig #g'|sed 's#$# off#g'|bash
```
方法2：
```bash
[root@AnSheng ~]# chkconfig --list|grep 3:on|awk '{print $1}'|sed -r 's#(.*)#chkconfig \1 off#g'|bash
```
方法3：
```bash
[root@AnSheng ~]# chkconfig --list|grep 3:on|grep -vE "crond|sshd|network|rsyslog" |awk '{print $1}'|sed -r 's#(.*)#chkconfig \1 off#g'|bash
```
方法4：
```bash
[root@AnSheng ~]# for name in `chkconfig --list|grep 3:on|grep -vE "crond|sshd|network|rsyslog" |awk '{print $1}'`;do chkconfig $name off;done
```
方法5：
```bash
[root@AnSheng ~]# for n in `chkconfig --list|grep 3:on|awk '{print $1}'`;do chkconfig --level 3 $n off;done
```
启动需要的服务
```bash
[root@AnSheng ~]# for n in crond network rsyslog sshd ;do chkconfig --level 3 $n on;done
```
检查开机自启动的服务器
```bash
[root@AnSheng ~]# chkconfig --list|grep 3:on
crond 0:off 1:off 2:off 3:on 4:off 5:off 6:off
network 0:off 1:off 2:off 3:on 4:off 5:off 6:off
rsyslog 0:off 1:off 2:off 3:on 4:off 5:off 6:off
sshd 0:off 1:off 2:off 3:on 4:off 5:off 6:off
```

### 三. 关闭iptables防火墙

```bash
[root@AnSheng ~]# /etc/init.d/iptables stop
iptables: Setting chains to policy ACCEPT: filter [ OK ]
iptables: Flushing firewall rules: [ OK ]
iptables: Unloading modules: [ OK ]
```
或者
```bash
[root@AnSheng ~]# service iptables stop
```
关闭开机自启动
```bash
[root@AnSheng ~]# chkconfig iptables off
```
检查
```bash
[root@AnSheng ~]# /etc/init.d/iptables status
 iptables: Firewall is not running.
[root@AnSheng ~]# chkconfig --list iptables
 iptables 0:off 1:off 2:off 3:off 4:off 5:off 6:off
```

### 四. Linux最小化原则

最小化原则：既多一事不如少一事，具体为：

1) 安装Linux系统最小化，既选包最小化，yum安装软件最小化；

2) 开机自启动程序服务最小化，既无用的服务不启动（少开一扇窗）

3) 操作命令最小化原则，rm –f test.txt,不用rm –rf test

4) 登陆Linux最小化，禁止root从远程终端登录，平时没有需求不用root，用普通用户登录。

5) 文件及目录的权限设置最小化。

6) 各种网络服务，系统配置参数合理，不要最大化。

### 五. 更改ssh服务端远程登录的配置

SSHD的配置文件在/etc/ssh/目录下

ssh_config：客户端

sshd_config：服务端
```bash
[root@AnSheng ssh]# vim sshd_config
```
修改的内容
```bash
Port 61482               #更改SSH远程连接的端口
PermitRootLogin no       #设置是否允许root通过ssh登录，这个选项从安全角度来讲应设成"no"。
PermitEmptyPasswords no  #设置是否允许用口令为空的帐号登录。
UseDNS no                #关闭UseDNS加速SSH登录
GSSAPIAuthentication no  #是否允许使用基于GSSAPI的用户认证.默认值为"no".仅用于SSH-2.
[root@AnSheng ssh]# /etc/init.d/sshd restart
Stopping sshd: [ OK ]
Starting sshd: [ OK ]
```
当前连接不重启不会中断，占用的连接没断开不会断开

### 六. 通过sudo管理权限

让一个用户可以拥有原本他没有执行这个命令的权限，这个命令的权限可能是其他用户的，它可以用其他用户执行，用sudo执行的权限都是需要当前执行sudo用户的密码。

sudo 是一种程序，用于提升用户的权限，在linux中输入sodu就是调用这个程序提升权限，是一个命令解析器，sudo cd是错误的，因为cd是内置的，不是系统里面的，sudo可以运行系统带的命令，但无法用系统中一个软件中的命令

创建一个普通用户
```bash
[root@AnSheng ~]# useradd AnSheng
```
非交互式设置密码
```bash
[root@AnSheng ~]# echo "123456"|passwd --stdin AnSheng
Changing password for user AnSheng.
passwd: all authentication tokens updated successfully.
#把echo的输出通过—stdin参数传输为zhanghui这个用户的密码
```
```bash
[root@AnSheng ~]# su - AnSheng
[AnSheng@AnSheng ~]$ whoami
AnSheng
```
小提示：

1)超级管理员用户切换到普通用户不需要密码，普通用户切换普通用户需要密码，普通用户切换超级管理员用户也需要密码

2)普通用户的权限比较小，只能做基本的信息查看，无法更改（就像臣子进皇宫，我在皇宫里面无论做什么都需要向皇上请示，只有授权了才可以访问）

3)#超级管理员 $普通用户
```bash
[root@AnSheng ~]# visudo
#在98行下面添加以下内容
AnSheng ALL=(ALL) ALL
#如果是命令使用下面的方式：
AnSheng ALL=(ALL) /bin/touch（多个权限用“，”分开，ALL所有权限，NOPASSWD: ALL免密码）
```
查看设置sudo用户的权限
```bash
[root@AnSheng ~]# su - AnSheng
[AnSheng@AnSheng ~]$ sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

 #1) Respect the privacy of others.
 #2) Think before you type.
 #3) With great power comes great responsibility.

[sudo] password for AnSheng: #输入密码

Matching Defaults entries for AnSheng on this host:
 requiretty, !visiblepw, always_set_home, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS", env_keep+="MAIL PS1
 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY
 LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",
 secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User AnSheng may run the following commands on this host:
 (ALL) ALL
```

### 七. 修改系统字符集
什么是字符集？

简单来说字符集就是一套文字符号及其编码，常用的字符集有GBK和UTF-8

Linux默认字符集路径：/etc/sysconfig/i18n
```bash
[root@AnSheng ~]# echo $LANG           #查看当前语言
[root@AnSheng ~]# locale -a|grep zh    #查看已安装语言包，如果没有中文语言包就安装中文语言包
[root@AnSheng ~]# LANG="zh_CN.gb18030" #修改当前字符集
[root@AnSheng ~]# source /etc/sysconfig/i18n  #让其生效
[root@AnSheng ~]# vi /etc/sysconfig/i18n      #修改字符集
[root@AnSheng ~]# source /etc/sysconfig/i18n  #让其永久生效,Linux字符集和远程连接设置的字符集要对应。
[root@AnSheng ~]# cp /etc/sysconfig/i18n /etc/sysconfig/i18n.oldboy.ori
[root@AnSheng ~]# vi /etc/sysconfig/i18n
[root@AnSheng sysconfig]# source /etc/sysconfig/i18n
[root@AnSheng sysconfig]# echo $LANG
zh_CN.UTF-8
[root@AnSheng sysconfig]# cat /etc/sysconfig/i18n
#LANG="en_US.UTF-8"
LANG="zh_CN.UTF-8"
SYSFONT="latarcyrheb-sun16"
```

### 八. 设置Linux系统时间每五分钟同步一次

```bash
[root@AnSheng ~]# crontab -l
[root@AnSheng ~]# echo '*/5 * * * * /usr/sbin/ntpdate time.nist.gov /dev/null 2&amp;1' > /var/spool/cron/root
[root@AnSheng ~]# crontab -l
*/5 * * * * /usr/sbin/ntpdate time.nist.gov /dev/null 2&amp;1
```

### 九. 修改文件描述符

文件描述符在形式上是一个非负整数。实际上，它是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。
```bash
[root@AnSheng ~]# echo '* - nofile 65535 ' > /etc/security/limits.conf
[root@AnSheng ~]# ulimit -n
65535
#需要退出重新登陆下
```
还有一种方法就是直接把ulimit -SHn 65535命令加入到/etc/rc.local，然后每次重起生效。
```bash
[root@AnSheng ~]# cat /etc/rc.local EOF
#-S use the `soft' resource limit
#-H use the `hard' resource limit
#-n the maximum number of open file descriptors
ulimit -HSn 65535
#-s the maximum stack size
ulimit -s 65535
EOF
```

### 十. 隐藏Linux版本信息显示

清空/etc/issue
```bash
[root@AnSheng ~]# > /etc/issue
[root@AnSheng ~]# cat /etc/issue
[root@AnSheng ~]# cat /dev/null /etc/issue
```

### 十一. 配置国内yum源

本次以阿里云的yum源为例
```bash
[root@AnSheng ~]# cd /etc/yum.repos.d/
[root@AnSheng yum.repos.d]# mkdir repo
[root@AnSheng yum.repos.d]# mv * repo
mv: cannot move `repo' to a subdirectory of itself, `repo/repo'
[root@AnSheng yum.repos.d]# wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-6.repo
--2015-11-26 21:31:23-- http://mirrors.aliyun.com/repo/epel-6.repo
Resolving mirrors.aliyun.com... 115.28.122.210, 112.124.140.210
Connecting to mirrors.aliyun.com|115.28.122.210|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1083 (1.1K) [application/octet-stream]
Saving to: “/etc/yum.repos.d/epel.repo”

100%[==========================================================================================================] 1,083 --.-K/s in 0s

2015-11-26 21:31:23 (196 MB/s) - “/etc/yum.repos.d/epel.repo” saved [1083/1083]

[root@AnSheng yum.repos.d]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
--2015-11-26 21:31:29-- http://mirrors.aliyun.com/repo/Centos-6.repo
Resolving mirrors.aliyun.com... 115.28.122.210, 112.124.140.210
Connecting to mirrors.aliyun.com|115.28.122.210|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2572 (2.5K) [application/octet-stream]
Saving to: “/etc/yum.repos.d/CentOS-Base.repo”

100%[==========================================================================================================] 2,572 --.-K/s in 0s

2015-11-26 21:31:30 (489 MB/s) - “/etc/yum.repos.d/CentOS-Base.repo” saved [2572/2572]

[root@AnSheng yum.repos.d]# yum clean all  #清楚缓存
Loaded plugins: fastestmirror, security
Cleaning repos: base epel extras updates
Cleaning up Everything
Cleaning up list of fastest mirrors
[root@AnSheng yum.repos.d]# yum makecache  #重新生成缓存
```