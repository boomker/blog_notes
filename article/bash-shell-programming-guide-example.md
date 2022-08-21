# Bash Shell编程指南之范例

- 使用for循环在`/tmp`目录下通过随机小写10个字母批量创建10个html文件

```bash
#!/bin/bahs

[ -d /tmp ] || /bin/mkdir /tmp

for ((i=1;i<11;i++)); do
  S=$(/bin/cat /proc/sys/kernel/random/uuid | /bin/sed 's#[0-9]##g' | /bin/sed 's#-##g' | /bin/cut -c 1-10)
  if [ ${#S} -eq 10 ];then
     /bin/touch /tmp/${S}_ansheng.html
  else
     ((i=i-1))
  fi
done
```

- 将以上文件名中的ansheng全部改成as(用for循环实现),并且html改成大写

第一种
```bash
#!/bin/bash
for N in `/bin/ls /tmp/`; do
  NAME=$(/bin/echo "$N"|/bin/awk -F "[_.]+" '{print $1}')
  /bin/mv /tmp/$N /tmp/${NAME}_as.HTML
done
```
第二种
```bash
#!/bin/bash
for N in `/bin/ls /tmp/`; do
NAME=$(echo $N | sed -r 's#(.*)_(.*).(.*)#\1.as_HTML#g')
mv /tmp/$N /tmp/$NAME
done
```

- 批量创建10个系统帐号`as01-as10`并设置密码（密码为随机8位字符串）

```bash
#!/bin/bash
for N in {01..10}; do
PASS=$(/usr/bin/openssl rand -base64 4)
  /usr/sbin/useradd ansheng$N
  /bin/echo "$PASS"|/usr/bin/passwd --stdin ansheng$N &> /dev/null
  /bin/echo "$PASS ansheng$N" >> /root/userfile
done
```

- 写一个脚本，实现判断`10.0.0.0/24`网络里，当前在线用户的IP有哪些（方法有很多）

for循环方式
```bash
#!/bin/bash
for n in `seq 254`
do
 /bin/ping -c2 10.0.0."$n">/dev/null 2>&1
 if [ "$?" -eq 0  ];then
   echo "10.0.0."$n" is up" >>/root/upip.txt
  else
   echo "10.0.0."$n" is down" >>/root/downip.txt
 fi
done
```
while方式
```bash
#!/bin/bash
I=1
RETVAL=0
while [ $I -lt 255 ]; do
  /bin/ping -c 2 -w 2 10.0.0.$I &>/dev/null
  RETVAL=$?
  if [ $RETVAL -eq 0 ];then
     echo -e "\033[32m 10.0.0.$I is up. \033[0m"
  else
  echo -e "\033[31m 10.0.0.$I is down. \033[0m"
  fi
  let I=I+1
done
```
until方式
```bash
#!/bin/bash
I=1
RETVAL=0
until [ $I -gt 254 ]; do
  /bin/ping -c 2 -w 2 10.0.0.$I &>/dev/null
  RETVAL=$?
  if [ $RETVAL -eq 0 ];then
     echo -e "\033[32m 10.0.0.$I is up. \033[0m"
  else
     echo -e "\033[31m 10.0.0.$I is down. \033[0m"
  fi
  let I=I+1
done
```

- 实现对MySQL数据库进行分库备份，脚本实现

```bash
#!/bin/bash
EXEC='/application/mysql/bin/mysql'
DUMP='/application/mysql/bin/mysqldump'
USER='-uroot -p3306'
SOCK='-S /data/3306/mysql.sock'
DATA=$($EXEC $USER $SOCK -e "show databases;"|sed 1d|grep -v "schema")
for N in $DATA; do
    $DUMP $USER $SOCK -B $N -F --master-data=2 --events --single-transaction |gzip >/opt/${N}_$(date +%F).sql.gz
done
```

- 实现对MySQL数据库进行分库加分表备份，脚本实现

```bash
#!/bin/bash
EXEC='/application/mysql/bin/mysql'
DUMP='/application/mysql/bin/mysqldump'
USER='-uroot -p3306'
SOCK='-S /data/3306/mysql.sock'
DATA=$($EXEC $USER $SOCK -e "show databases;"|sed 1d|grep -v "schema")
for N in $DATA; do
[ -d /opt/$N ] || mkdir /opt/$N
TAB=$($EXEC $USER $SOCK -e "use $N;show tables;"|sed 1d)
  for B in $TAB; do
      $DUMP $USER $SOCK $N $B -F --master-data=2 --events --single-transaction |gzip >/opt/${N}/${B}_$(date +%F).sql.gz
  done
done
```

- bash for循环打印下面这句话中字母数不大于6的单词

```
I am ansheng teacher welcome to ansheng training class.
```

第一种

```bash
#!/bin/bash
ECHO=$(echo "I am ansheng teacher welcome to ansheng training class."|tr " " "\n")
for N in $ECHO; do
if [ ${#N} -lt 6 ];then
   echo "$N"
fi
done
```
第二种
```bash
#!/bin/bash
ECHO=$(echo "I am ansheng teacher welcome to ansheng training class."|tr " " "\n")
for N in $ECHO; do
if [ `echo $N|wc -L` -lt 6 ];then
   echo "$N"
fi
done
```

- 开发shell脚本分别实现以脚本传参以及read读入的方式比较2个整数大小。以屏幕输出的方式提醒用户比较结果。

> 一共是开发2个脚本。当用脚本传参以及read读入的方式需要对变量是否为数字、并且传参个数做判断。

read方式
```bash
#!/bin/bash
read -p "Please input two integers:" a b
if [ -z $a -o -z $b ];then
   echo "Error:Please input two integers."
   exit 1
fi
expr $a + 1 &>/dev/null
if [ $? -ne 0 ];then
   echo "The first number must be an integer."
   exit 2
fi
expr $b + 1 &>/dev/null
if [ $? -ne 0 ];then
   echo "The second number must be an integer."
   exit 2
fi
if [ $a -lt $b ];then
   echo "$a < $b"
elif [ $a -gt $b ];then
   echo "$a > $b"
else
    echo "$a = $b"
fi
```
脚本传参
```bash
#!/bin/bash
a=$1
b=$2
if [ -z $b ];then
   echo "Error:Please input two integers."
   exit 1
fi
if [ -z $a ];then
   echo "Error:Please input two integers."
   exit 1
fi
expr $a + 1 &>/dev/null
if [ $? -ne 0 ];then
   echo "The first number must be an integer."
   exit 2
fi
expr $b + 1 &>/dev/null
if [ $? -ne 0 ];then
   echo "The second number must be an integer."
   exit 2
fi
if [ $a -lt $b ];then
   echo "$a < $b"
elif [ $a -gt $b ];then
   echo "$a > $b"
else
    echo "$a = $b"
fi
```

- 打印选择菜单，一键安装Web服务

要求：

1. 当用户输入1时，输出“startinstalling lamp.”然后执行/server/scripts/lamp.sh，脚本内容输出"lampis installed"后退出脚本；
2. 当用户输入2时，输出“startinstalling lnmp.”然后执行/server/scripts/lnmp.sh输出"lnmpis installed"后退出脚本;
3. 当输入3时，退出当前菜单及脚本；
4. 当输入任何其它字符，给出提示“Input error”后退出脚本。
5. 要对执行的脚本进行相关条件判断，例如：脚本是否存在，是否可执行等。 

```bash
#!/bin/bash
cat <<END
 1.[install lamp]
 2.[install lnmp]
 3.[exit]
END
read -p "pls input the num you want:" a
case "$a" in
  1)
     echo "startinstalling lamp."
     sleep 2
     if [ -f /server/scripts/lamp.sh ];then
        [ -x /server/scripts/lamp.sh ] || {
	  echo "Did not execute permissions"
	  exit 3
	}
	/bin/sh /server/scripts/lamp.sh
     else
       echo "/server/scripts/lamp.sh  No such file"
     fi
     ;;
  2)
     echo "startinstalling lnmp."
     sleep 2
     if [ -f /server/scripts/lnmp.sh ];then
        [ -x /server/scripts/lnmp.sh ] || {
	  echo "Did not execute permissions"
	  exit 3
	}
	/bin/sh /server/scripts/lnmp.sh
     else
       echo "/server/scripts/lnmp.sh  No such file"
     fi
     ;;
  3)
     exit 0
     ;;
  *)
     echo "Input error."
     exit 1
esac
```

- 监控web/db服务是否正常

```bash
#!/bin/bash
while :;do
 STATUS=$(curl -I -s -w "%{http_code}" -o /dev/null 172.16.1.20)
  if [ "$STATUS" -eq "200" ];then
   echo "Nginx is running..."
  else
   echo "Nginx is stopped..."
  fi
sleep 60
done
```

```bash
#!/bin/bash
#STATUS=$(netstat -tulnp|grep 80|wc -l)
#STATUS=$(lsof -i :80|wc -l)
#STATUS=$(ps -ef|grep nginx|grep -v ""grep|wc -l)
while :;do
. /etc/init.d/functions
STATUS=$(lsof -i :80|wc -l)
  if [ "$STATUS" -eq 0 ];then
     sleep 1
     echo "nginx is stopped..."
     sleep 1
     echo "Try to start nginx..."
     /application/nginx/sbin/nginx
     sleep 1
       STATUS=$(lsof -i :80|wc -l)
       if [ "$STATUS" -eq 0 ];then
         action "Start nginx" /bin/false
       else
         action "Start the nginx" /bin/true
        fi
       sleep 2
  else
     echo "Nginx is running..."
     sleep 2
  fi
sleep 60
done
```

```bash
#!/bin/bash
EXEC='/application/mysql/bin/mysql -uroot -p3306 -S /data/3306/mysql.sock'
while :; do
$EXEC -e "insert into status.test(id) values('1');" &>/dev/null
M=$($EXEC -e "select * from status.test;" 2>/dev/null|wc -l)
if [ $M == 2 ];then
  echo "mysql ok."
  $EXEC -e "delete from status.test;"
else
  echo "mysql down."
fi
sleep 60
done
```

- 监控web站点目录（/var/html/www）下所有文件是否被恶意篡改（文件内容被改了），如果有就打印改动的文件名（发邮件），定时任务每3分钟执行一次(10分钟时间完成)。

```bash
#!/bin/bash
#find /var/html/www/ -type f|xargs md5sum > /root/md5.log
while :;do
 for N in `find /var/html/www/ -type f` ;do
   if [ `md5sum $N |awk '{print $1}'` == `grep $N /root/md5.log|awk '{print $1}'` ];then
      echo "$N ok"
   else
      echo "$N no"
   fi
 done
sleep 180
done
```

- 写网络服务独立进程模式下rsync的系统启动脚本

例如

```bash
$ /etc/init.d/rsyncd{start|stop|restart} 
```

要求

1. 要使用系统函数库技巧。
2. 要用函数，不能一坨SHI的方式。
3. 可被chkconfig管理。

```bash
#!/bin/bash
#
# rsynd          Start/Stop the rsyncd clock daemon.
#
# chkconfig: - 66 38
# processname: rsyncd
# config: /etc/rsyncd.conf 
# pid: $(ps -ef|grep "rsync --daemon"|grep -v "grep"|awk '{print $2}')
#
# Source function library.
. /etc/init.d/functions
STATUS=$(lsof -i :873|wc -l)
PID=$(ps -ef|grep "rsync --daemon"|grep -v "grep"|awk '{print $2}')
CONFIG='/etc/rsyncd.conf'
RETVAL=0
#Start rsync service
start_rsync (){
STATE=$(lsof -i :873|wc -l)
  [ $UID -eq 0 ] || exit 4
  [ -f $CPNFIG ] || exit 5
  if  [ $STATE -eq 0 ];then
      rsync --daemon
      RETVAL=$?
      action "Stopping rsync:" /bin/true
  else
      echo "Running rsync.."
  fi
  return $RETVAL
}
#Stop rsync service
stop_rsync (){
  [ $UID -eq 0 ] || exit 4
  if [ $STATUS -eq 0 ];then
      action "Stopping rsync:" /bin/false
  else
      pkill rsync
      RETVAL=$?
      action "Stopping rsync:" /bin/true
  fi
  return $RETVAL
}
#Check the rsync service status
status_rsync (){
   if [ $STATUS -eq "0" ];then
       echo "rsyncd is stopped"
   else
       echo "rsyncd (pid  $PID) is running..."
       RETVAL=$?
   fi
   return $RETVAL
}
#Get help
help_rsync (){
    echo "Usage: $0 {start|stop|restart|status|help}"
}

case "$1" in
  start)
         start_rsync
         ;;
  stop)
         stop_rsync
         ;;
  restart)
         stop_rsync
         sleep 1
         start_rsync
         ;;
  status)
         status_rsync
         ;;
  help)
         help_rsync
         ;;
  *)
         help_rsync
esac
```

- 抓阄

1. 执行脚本后，想去的同学输入英文名字全拼，产生随机数01-99之间的数字，数字越大就去参加项目实践，前面已经抓到的数字，下次不能在出现相同数字。
2. 第一个输入名字后，屏幕输出信息，并将名字和数字记录到文件里，程序不能退出继续等待别的学生输入。

```bash
#!/bin/bash
while :; do
read -p "Please come in and go out English name:" name
[ -z $name ] && continue
echo "$name $(/bin/cat /proc/sys/kernel/random/uuid|/bin/sed 's#[a-z]##g'|/bin/cut -c 1-2)"
done
```

- 已知下面的字符串是通过RANDOM随机数变量`md5sum|cut-c 1-8`截取后的结果，请破解这些字符串对应的md5sum前的RANDOM对应数字？

```
21029299
00205d1c
a3da1677
1f6d12dd
890684b
```
创建随机数
```bash
#!/bin/bash
for N in {0..32767}; do
  echo "`echo "$N"|md5sum|cut -c 1-8` $N" >>/root/md5.log
done
```
grep查找
```bash
#!/bin/bash
for I in `cat /root/random`; do
   grep "$I" /root/md5.log 
done
```

- 批量检查多个网站地址是否正常

```bash
#!/bin/bash

URL=(
http://www.baidu.com
http://www.taobao.com
)
while :; do
for N in ${URL[*]}; do
  Z=$(curl -I -s -w "%{http_code}" -o /dev/null $N)
  if [ "$Z" -eq "200" ];then
    echo "$N OK."
  else
    echo "$N down."
  fi
  done
sleep 10
done
```