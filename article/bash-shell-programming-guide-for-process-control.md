## Bash Shell编程指南之流程控制

## if

`if`结构

```bash
#!/bin/bash

if command
then
   block of statements
fi

-------------------------

if  [[ expression  ]]
then
   block of statements
fi
```

### `if/else`结构

```bash
#!/bin/bash

if  command
then
   block of statements
else
   block of statements
fi

-------------------------

if  [[ expression ]]
then
   block of statements
else
   block of statements
fi

-------------------------

if  ((  numeric expression ))
then
   block of statements
else
   block of statements
fi
```

### `if/else/else if`结构

```bash
#!/bin/bash

if  command
then
   block of statements
elif  command
then
   block of statements
else if  command
then
   block of statements
else
   block of statements
fi

-------------------------

if  [[ expression ]]
then
   block of statements
elif  [[  expression ]]
then
   block of statements
else if  [[  expression ]]
then
   block of statements
else
   block of statements
fi

--------------------------

if  ((  numeric expression ))
then
   block of statements
elif  ((  numeric expression ))
then
   block of statements
else if  ((numeric expression))
then
   block of statements
else
   block of statements
fi
```

## case

```bash
#!/bin/bash

case variable_name in
   pattern1)
      statements
         ;;
   pattern2)
      statements
         ;;
   pattern3)
         ;;
esac

--------------------------

case "$color" in
   blue)
      echo $color is blue
         ;;
   green)
      echo $color is green
         ;;
   red|orange)
      echo $color is red or orange
         ;;
   *) echo "Not a matach"
         ;;
esac
```

### 范例

```bash
$ cat t.sh
#!/bin/bash

case "$1" in
 1) echo "1"
;;
 2) echo "2"
;;
 *) echo "Low"
esac
$ bash t.sh
Low
$ bash t.sh 1
1
$ bash t.sh 2
2
$ bash t.sh 4
Low
```

- 根据用户的选择输入判断是那种水果并加上不同的颜色。

```bash
#!/bin/bash

RED_COLOR='\E[1;31m'
GREEN_COLOR='\E[1;32m'
YELLOW_COLOR='\E[1;33m'
BLUE_COLOR='\E[1;34m'
RES='\E[0m'
while :;do
cat <<END
  1.apple
  2.pear
  3.banana
  4.cherry
  5.exit
END
read -p "Pls input 1 or 2 or 3 or 4:"  a
case "$a" in
  1)
   echo -e "$RED_COLOR apple $RES"
;;
  2)
   echo -e "$YELLOW_COLOR pear $RES"
;;
  3)
   echo -e "$YELLOW_COLOR banana $RES"
;;
  4)
   echo -e "$RED_COLOR  cherry $RES"
;;
  5)
   break
;;
  *)
   echo "Pls input 1 or 2 or 3 or 4"
esac
done
```

- 在脚本中通过函数给指定的字符内容加指定颜色

```bash
#!/bin/bash

RED_COLOR='\E[1;31m'
GREEN_COLOR='\E[1;32m'
YELLOW_COLOR='\E[1;33m'
BLUE_COLOR='\E[1;34m'
RES='\E[0m'

case "$1" in
   red)
       echo -e "$RED_COLOR "$1" $RES"
;;
   yellow)
       echo -e "$YELLOW_COLOR "$1" $RES"
;;
  blue)
       echo -e "$BLUE_COLOR "$1" $RES"
;;
  green)
       echo -e "$GREEN_COLOR "$1" $RES"
;;
  *)
       echo "Usage plus.sh content {red|yellow|blue|green}"
esac
```

```bash
#!/bin/bash
RED_COLOR='\E[1;31m'
GREEN_COLOR='\E[1;32m'
YELLOW_COLOR='\E[1;33m'
BLUE_COLOR='\E[1;34m'
PINK='\E[1;35m'
RES='\E[0m'

if [ $# -ne 1 ]
then
  echo "Usage $0 content {red|yellow|blue|green}"
  exit 1
fi

case "$1" in
  red|RED)
    echo -e "${RED_COLOR}$1${RES}"
    ;;
  yellow|YELLOW)
    echo -e "${YELLOW_COLOR}$1${RES}"
    ;;
  green|GREEN)
    echo -e "${GREEN_COLOR}$1${RES}"
    ;;
  blue|BLUE)
    echo -e "${BLUE_COLOR}$1${RES}"
    ;;
  pink|PINK)
    echo -e "${PINK_COLOR}$1${RES}"
    ;;
  *)
    echo "Usage $0 content {red|yellow|blue|green}"
    exit
esac
```

- case脚本模拟nginx服务启动关闭：`/etc/init.d/nginx {start|stop|restart|reload}`

```bash
$ cat check_mc.sh 
#!/bin/sh
printf "del key\r\n"|nc 127.0.0.1 11211 &>/dev/null
printf "set key  0 0 10 \r\nansheng0987\r\n"|nc 127.0.0.1 11211 &>/dev/null
McValues=`printf "get key\r\n"|nc 127.0.0.1 11211|grep ansheng0987|wc -l`
if [ $McValues -eq 1 ]
 then
   echo "memcached status is ok"
 else
   echo "memcached status is bad"
fi
```

```bash
#!/bin/bash
. /etc/init.d/functions
start_nginx(){
   if [ `netstat -tulnp|grep "nginx"|wc -l` -eq "0" ];then
       /application/nginx/sbin/nginx 2>/dev/null
       if [ "$?" -ne 0 ];then
         action "Starting nginx:" /bin/false
       else
         action "Starting nginx:" /bin/true
       fi
   else
       echo "nginx is running..."
   fi
}
stop_nginx(){
   if [ `netstat -tulnp|grep "nginx"|wc -l` -eq "0" ];then
       action "Stopping nginx:" /bin/false
   else
       pkill nginx
       action "Stopping nginx:" /bin/true 
   fi
}
status_nginx(){
   if [ `netstat -tulnp|grep "nginx"|wc -l` -eq "0" ];then
       echo "nginx is stopped"
   else
       echo "nginx (pid  $(cat /application/nginx/logs/nginx.pid)) is running..."
   fi
}
reload_nginx(){
   if [ `netstat -tulnp|grep "nginx"|wc -l` -eq "0" ];then
       echo "nginx is stopped"
   else
       kill -HUP `cat /application/nginx/logs/nginx.pid >/dev/null 2>&1` >/dev/null 2>&1
   fi
}
help_nginx(){
       echo "Usage: $0 {start|stop|restart|status|reload|configtest|help}"
}
configtest_nginx(){
   /application/nginx/sbin/nginx -t >/dev/null 2>&1
   if [ "$?" -eq "0" ];then
      echo "Syntax OK"
   else
      echo "$(/application/nginx/sbin/nginx -t)"
   fi
}

case "$1" in
 start)
        start_nginx
        ;;
 stop)
        stop_nginx
        ;;
 status)
        status_nginx
        ;;
 restart)
        stop_nginx
        sleep 2
        start_nginx
        ;;
 help)
        help_nginx
        ;;
 configtest)
        configtest_nginx
        ;;
 reload)
        reload_nginx
        ;;
   *)
        help_nginx
esac
```

- RSYNC启动关闭脚本

```bash
#!/bin/bash
. /etc/init.d/functions
JC=$(lsof -i :873|wc -l)
start_rsync(){
 if  [ "$JC" -ne 0 ];then
     echo "Running rsync.."
 else
     rsync --daemon
     action "Stopping rsync:" /bin/true
 fi
}
#stop rsync
stop_rsync(){
 if [ "$JC" -ne 0 ];then
     pkill rsync
     action "Stopping rsync:" /bin/true
 else
     action "Stopping rsync:" /bin/false
 fi
}
#restart rsync
case "$1" in
  start) start_rsync
;;
  stop) stop_rsync 
;;
  restart) stop_rsync
	   sleep 2
           start_rsync
;;
  *) echo "USAGE：$0 {start|stop|restart}"
esac
```

## 循环

循环有四种类型: `while`、`until`、`for`、`select`

### `while`循环,只要表达式为真，循环体就会被一直执行。

```bash
#!/bin/bash

while command
do
   block of statements
done 

-------------------------

while [[ string expression ]]
do
   block of statements
done

-------------------------

while (( numeric expression ))
do
    block of statements
done
```

#### 范例

```bash
$ sh while02.sh 
5050
$ cat while02.sh 
#!/bin/bash
i=1
sum=0
while [ $i -le 100 ]
do
  let sum=sum+i
  let i=i+1
done
echo $sum
```

```bash
#!/bin/bash

i=1
sum=0
while ((i < 101))
do
  ((sum=sum+i))
  ((i++))
done

echo $sum
```

### for循环

循环指定的所有元素，循环完毕之后就推出

```bash
#!/bin/bash

for variable in word_list
do
   block of statements
done

--------------------------                  

for color in red green b
do
   echo $color
done  
```

#### 范例

- 使用for循环在/ansheng目录下批量创建10个文件，名称依次为：

```
ansheng-1.html
ansheng-2.html
ansheng-3.html
......
ansheng-10.html
```

```bash
#!/bin/bash

for N in `seq 10`; do
 touch ansheng-$N.html
 echo $N > ansheng-$N.html
done
```

- 用for循环实现将以上文件名中的ansheng全部改成linux，并且扩展名改成大写。要求：for循环的循环体不能出现ansheng字符串。

```bash
#!/bin/bash

for I in `ls /ansheng|grep -v "sh$"|awk -F "-" '{print $1}'`; do
 for N in `seq 10`;do
  mv $I-$N.html linux-$N.HTML 2>/dev/null
  [ $? -ne 0 ]&&exit
 done
done
```

- 批量创建10个系统帐号ansheng01-ansheng10并设置密码（密码为随机8位字符串）

```bash
#!/bin/bash

for N in {01..10}; do
  useradd ansheng$N
  PASS=$(awk -F "-" '{print $1}' /proc/sys/kernel/random/uuid)
  echo "$PASS"|passwd --stdin ansheng$N
  echo "ansheng$N $PASS" >>/root/user.txt
done
```

- 手机充值10元，每发一次短信（输出当前余额）花费1角5分钱，当余额低于1角5分钱不能发短信，提示余额不足，请充值，请用while语句实现。

```bash
#!/bin/bash
i=1000
while [ $i -gt 15 ];do
 ((i=i-15))
 echo $i
done
echo "NO"
```

> 单位换算。统一单位，统一成整数，10元=1000分，1角5分=15分

- 每两秒检测一次URL是否正常

```bash
#!/bin/bash

while :;do
Z=$(curl -I -s -w "%{http_code}" -o /dev/null $1)
if [ "$Z" -eq "200" ];then
  echo "$1 OK."
else
  echo "$1 down."
fi
  sleep 2
done
```

- 已知下面的字符串是通过RANDOM随机数md5sum后的结果，请破解这些字符串对应的md5sum前的数字？

```
2102929901ee1aa769d0f479d7d78b05
00205d1cbbeb97738ad5bbdde2a6793d
a3da1677501d9e4700ed867c5f33538a
1f6d12dd61b5c7523f038a7b966413d9
890684ba3685395c782547daf296935f
```

```bash
#!/bin/bash
for N in {0..32767}; do
W=$(echo $N)
S=$(echo $N | md5sum | awk '{print $1}')
if [ "$S" == "2102929901ee1aa769d0f479d7d78b05" ];then
   echo $W >>/root/root.log
   continue
elif [ "$S" == "00205d1cbbeb97738ad5bbdde2a6793d" ];then
   echo $W >>/root/root.log
   continue
elif [ "$S" == "a3da1677501d9e4700ed867c5f33538a" ];then
   echo $W >>/root/root.log
   continue
elif [ "$S" == "1f6d12dd61b5c7523f038a7b966413d9" ];then
   echo $W >>/root/root.log
   continue
elif [ "$S" == "890684ba3685395c782547daf296935f" ];then
   echo $W >>/root/root.log
   continue
else
  continue
fi
done
```

or

```bash
for n in {0..32767}
do
  echo "`echo $n|md5sum` $n" >>zhiwen.log
done

exec < fanzui1.log
while read line
do
    grep "$line" zhiwen.log
done
```

- 随机数获取方法

```bash
$ echo $RANDOM
28691
$ 
$ openssl rand -base64 8
IBvOphJ7l3g=
$ head /dev/urandom|cksum
4123889536 2383
$  cat /proc/sys/kernel/random/uuid
26fdb902-06d1-4e85-a607-734e74e3d7b7
```

## 函数

一般会把重复执行的代码块包装到一个函数中

```bash
#!/bin/bash

function_name() {
   block of code
}

function  function_name {
   block of code
}

------------------------

function  lister {
   echo Your present working directory is `pwd`
   echo Your files are:
   ls
}
```

### 范例

- 一个简单带色彩的启动脚本

```bash
#!/bin/bash
. /etc/init.d/functions

if [ $# -ne 1 ];then
  echo "USAGE：$0 {start|stop|restart}"
  exit 1
fi

if [ "$1" == "start" ];then
  action "start ... " /bin/true
elif [ "$1" == "stop" ];then
  action "stop ... " /bin/true
elif [ "$1" == "restart" ];then
  action "restart ... " /bin/true
else
  echo "USAGE：$0 {start|stop|restart}"
  exit 1
fi
```

- 创建个函数并执行

```bash
$ cat t.sh
#!/bin/bash

test(){
  echo "hello word!"
}
$ bash t.sh
hello word!
```

```bash
$ cat t.sh
#!/bin/bash

function Check_Url() {
  curl -I -s $1 | head -1 && return 0 || eturn 1
}
Check_Url blog.ansheng.me
$ bash t.sh
HTTP/1.1 301 Moved Permanently
```