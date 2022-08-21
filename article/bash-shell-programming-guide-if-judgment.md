## Bash Shell编程指南之if判断

### 单分支结构语法

- 语法结构

```bash
if [ 条件 ]
then
  指令
fi
```

或者

```bash
if [ 条件 ];then
	指令
fi
```

> 分号相当于命令换行，上面两种语法等同

特殊写法：`if[ -f “$file1” ];then echo 1;fi`相当于：`[ -f “$file1” ] && echo 1`

```bash
if [ -f "$file1" ];then
	echo 1
fi
```

### 单分支范例

用`if`比对两个文件的大小

```bash
#!/bin/bash
a=$1
b=$2
if [ -z "$a" -o -z "$b" ];then 
 echo "Error: Please Output integer." 
 exit 1
fi

expr "$a" + 1  >/dev/null 2>&1

if [ "$?" -ne 0 ];then
  echo "Error: a must be an integer."
  exit 1
fi

expr "$b" + 1 >/dev/null 2>&1

if [ "$?" -ne 0 ];then
  echo "Error: b must be an integer."
  exit 1
fi

[ "$a" -gt "$b" ] && echo "$a > $b"
[ "$a" -lt "$b" ] && echo "$a < $b"
[ "$a" -eq "$b" ] && echo "$a = $b"
```
```bash
# 使用read传参
$ cat if_read.sh 
#!/bin/bash
read -p "请输入两个整数:" a b

if [ -z "$a" -o -z "$b" ];then 
 echo "Error: Please Output integer." 
 exit 1
fi

expr "$a" + 1  >/dev/null 2>&1

if [ "$?" -ne 0 ];then
  echo "Error: a must be an integer."
  exit 1
fi

expr "$b" + 1 >/dev/null 2>&1

if [ "$?" -ne 0 ];then
  echo "Error: b must be an integer."
  exit 1
fi

[ "$a" -gt "$b" ]&&echo "$a > $b"
[ "$a" -lt "$b" ]&&echo "$a < $b"
[ "$a" -eq "$b" ]&&echo "$a = $b"
```
当内存低于`100M`的时候发邮件报警，定时任务每三分钟过执行一次
```bash
$ cat if_dan.sh 
#!/bin/bash
RAM=$(free -m | sed -n 3p | awk '{print $4}')

if [ "$RAM" -lt 100 ];then
  echo "The remaining memory space is less than 100M"|mail -s 'Caveat' root@shell
  exit 0
fi
$ tail -1 /var/spool/cron/root 
*/3 * * * * /bin/sh /server/scripts/02/if_dan.sh >/dev/null 2>&1
```

> 内存、磁盘、NFS、MySQL、WEB这些都可以做监控。

### if双分支结构语法

语法结构

```bash
if [ 条件 ]
then
  指令集
else
  指令集
fi
```
> 特殊写法：`if [ -f "$file1" ];then echo 1;else echo 0;fi `相当于：`[ -f "$file1" ] && echo 1 || echo 0`

### if多分支结构语法

- 语法结构
```bash
if [ 条件 ]
then
  指令
elif [ 条件 ]
then
  指令
else
  指令
fi
```

1. 注意多分支`elif`的写法`elif [条件];then`，不要落下了`then`
2. 结尾的else后面没有then

###  多分支范例

使用read传参的方式进行判断比较

```bash
#!/bin/bash
read -p "请输入两个整数：" a b

if [ -z "$a" -o -z "$b" ];then 
 echo "Error: Please Output integer." 
 exit 1
fi

expr "$a" + 1  >/dev/null 2>&1
if [ "$?" -ne 0 ];then
  echo "Error: a must be an integer."
  exit 1
fi

expr "$b" + 1 >/dev/null 2>&1
if [ "$?" -ne 0 ];then
  echo "Error: b must be an integer."
  exit 1
fi

if [ "$a" -gt "$b" ];then
  echo "$a > $b"
  exit 0
elif [ "$a" -lt "$b" ];then
  echo "$a < $b"
  exit 0
else
  echo "$a = $b"
  exit 0
fi
```
开发脚本实现如果`/server/scripts/`下面存在`if3.sh`就输出屏幕，如果不存在就自动创建`if3.sh`这个脚本
```bash
$ cat if-file.sh 
#!/bin/bash
FILEPATH=/server/scripts/if3.sh
FILENAME=$(ls $FILEPATH 2>/dev/null)

if [ "$?" -ne 0 ];then
  touch /server/scripts/if3.sh
  echo "create file ok."
  exit
fi

if [ "$?" -eq 0 ];then
  echo "$(cat $FILEPATH)"
  exit
fi
```

### 判断字符串是否为数字的多种方法

- 方法一：sed加正则表达式

```bash
$ [ $(echo 1 | sed 's/[0-9]//g') -a $(echo 2 | sed 's/[0-9]//g') ] && echo '参数正确！' || echo '参数必须是整数！'
```

- 方法二：变量的字符串替换加正则表达式

```bash
$ n=as1
$ [ -z $(echo "${n//[0-9]/}") ] && echo 1 || echo 0
0
$ n=1as1
$ [ -z $(echo "${n//[0-9]/}") ] && echo 1 || echo 0
0 # 前面的结果不为0，有非数字字符
$ n=111
$ [ -z $(echo "${n//[0-9]/}") ] && echo 1 || echo 0
1 # 前面没有非数字字符
```

- 法三：变量的子串替换加正则表达式（特殊判断思路）

```bash
$ [ -n $num -a $num = ${num//[0-9]/} ] && echo 0 || echo 1
1
$ num=a
$ [ -n $num -a $num = ${num//[0-9]/} ] && echo 0 || echo 1
0
```

- 法四：Expr计算判断

```bash
expr $1 + 0 >/dev/null 2>&1
[ $? -eq 0 ] && echo 1
```

- 方法5：利用`=~`符号判断

```bash
$ [[ a =~ [0-9] ]] && echo int || echo char
char
$ [[ 1 =~ [0-9] ]] && echo int || echo char
int
```

```bash
[[ $1 =~ ^[0-9]+$ ]] &&echo int ||echo char
```

### 扩展：判断字符串长度是不是为0的多种思路

- 字符串-z,-n表达式

```bash
[ -z "as" ] && echo 1 || echo 0
```

- 变量子串

```bash
${#arg} -eq 0
```

- expr

```bash
expr length "as"
[ `expr length "as"` -eq 0 ]
```

- wc统计

```bash
echo "as" | wc -L
```

### WEB服务监控手段

- 端口

1. 本地：ss,netstat,lsof
2. 远程：telnet,nmap,nc

- 本地进程数

```bash
$ ps -ef | grep apache | wc -l
9
```
- header(http code)curl -I返回200就Ok

```bash
$ curl -I -s 172.16.1.10|head -1
HTTP/1.1 403 Forbidden
$ curl -I 172.16.1.10 2>/dev/null|head -1
HTTP/1.1 403 Forbidden
$ curl -I -s -w "%{http_code}" -o /dev/null 172.16.1.10
403
$
```
- URL （wget,curl）

```bash
$ wget --spider --timeout=100 172.16.1.10
$ wget --spider --timeout=100 --tries=2 172.16.1.101
# 超时时间，重试次数
$ echo $?
# 看返回值
```

- 本地监控

```bash
$ lsof -i :80|wc -l
10
```
- 远程监控

```bash
$ nmap 172.16.1.10 -p 80 | grep open | wc -l
1
$ telnet 172.16.1.10 80
Trying 172.16.1.10...
Connected to 172.16.1.10.
Escape character is '^]'.
```