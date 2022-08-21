# Bash Shell编程指南之变量

## 变量基础及深入

### 什么是变量?

变量是一个用固定的字符串（也可能是字符数字等的组合），替代更多更复杂的内容，这个内容里可能还会包含变量和路径，字符串等其他的内容。使用变量最大的好处就是方便，当然，除了方便以外，很多时候在变成中使用变量也是必须的，否则就无法完成开发工作了。

```bash
$ ansheng='Hello, Python!'  # 创建变量ansheg
$ echo $ansheng  # 输出变量ansheg
Hello, Python!
```

- 变量类型

变量可分为两类：

1. 环境变量（全局变量），环境变量也可称为全局变量，可以在创建他们的shell及其派生出来的任意子教程shell中使用。
2. 普通变量（局部变量），普通变量只能在创建他们的`shell`函数或脚本中使用。

### 环境变量

环境变量用于定义shell的运行环境，保证Shell命令的正确执行，通过环境变量来确定登陆用户名、命令路径、终端类型、登陆目录等，所有的环境变量都是系统全局变量，可用于所有子进程中，这包括编辑器、Shell脚本和各类应用。

环境变量可以在命令行中设置，当用户退出时这些变量值也会丢失，因此最好在用户家目录下的`.bash_profile`文件中或全局配置`/etc/bashrc`,`/etc/profile`文件或者`/etc/profile.d/`中定义。将环境变量放入上述的文件中，每次用户登陆时这些变量值都将被初始化。

传统上，所有环境变量均为大写。环境变量应用于用户进程前，都应该用export命令导出，例如：`export AS=1`

环境变量可用在创建他们的shell和从该shell产生的任意子shell或进程中。他们通常被称为全局变量以区别局部变量。通常，环境变量应该大写。环境变量是已经用export内置命令导出的变量。

有一些环境变量，比如`HOME`、`PATH`、`SHELL`、`UID`、`USER`等，在用户登陆之前就已经被`/bin/login`程序设置好了。通常环境变量定义并保存在用户家目录下的`.bash_profile`文件中。

可以通过一下的命令查看环境变量

 1. env（只显示全局变量）
 2. set（所有的变量）
 3. declare（所有变量，包括函数、整数和已导出的）


- 环境变量设置的常用文件及区别

用户的环境变量配置

```bash
$ ll /root/.bash_profile 
-rw-r--r--. 1 root root 176 May 20  2009 /root/.bash_profile
$ ll /root/.bashrc 
-rw-r--r--. 1 root root 176 Sep 23  2004 /root/.bashrc
```

全局环境变量的配置

```bash
$ ll /etc/profile
-rw-r--r--. 1 root root 1796 Oct  2  2013 /etc/profile
$ ll /etc/bashrc 
-rw-r--r--. 1 root root 2681 Oct  2  2013 /etc/bashrc
```

用户登陆后加载的内容，可以在跳板机上操作，让每切换一次用户就显示当前登陆的USER and IP切换或者登陆用户时提示信息

```bash
$ cd /etc/profile.d/
$ cat hello.sh 
echo "this is Ubuntu"
$ exit
logout
$ sudo su -
this is Ubuntu
```

用户第一次登陆时提示，只能是字符串

```text
Last login: Thu Apr  2 12:15:21 2015 from 10.0.0.122
welcome to Ubuntu linux training !
$ cat /etc/motd 
welcome to Ubuntu linux training !
```

### 定义全局变量

```bash
$ export NAME=ansheng
$ NAME=ansheng
$ export NAME
$ export LANG=EN
$ declare -x NAME=ansheng
```

#### 显示与取消环境变量

设置与取消环境变量

```bash
$ export NAME=ansheng
$ echo $NAME
ansheng
$ unset NAME
$ echo $NAME

```

系统自带的一些环境变量

```bash
$ echo $HOME			用户登录时进入的目录
$ echo $UID			当前用户UID（用户标识）相当于id -u
$ echo $PWD			当前工作目录的绝对路径
$ echo $SHELL			当前SHELL
$ echo $USER			当前用户
```

#### 定义本地变量

本地变量在用户当前的Shell生存期额脚本中使用。例如，本地变量`ANSHENG`取值为`hello，这个值只在用户当前Shell生存期中有意义，如果在shell中启动另一个进程或退出，本地变量ANSHENG值无效。

- 普通字符串变量定义

```bash
变量名=value
变量名='value'
变量名="value"
命令变量定义
变量名=$()
变量名=``
```
- shell中变量明的要求

一般是由字母，数字，下划线组成，以字母开头

```bash
$ a=192.168.1.2
$ b='192.168.1.2'
$ c="192.168.1.2"
$ echo "a=$a"
a=192.168.1.2
$ echo "b=$b"
b=192.168.1.2
$ echo "c=${c}"
c=192.168.1.2
```
例2
```bash
$ a=192.168.1.2-$a
$ b='192.168.1.2-$a'
$ c="192.168.1.2-$a"
$ echo "a=$a"
a=192.168.1.2-192.168.1.2
$ echo "b=$b"
b=192.168.1.2-$a
$ echo "c=${c}"
c=192.168.1.2-192.168.1.2-192.168.1.2
```

**提示：**

1. 第一种定义a变量的方式是直接指定变量内容，内容一般为简单连续的数字、字符串、路径明等。
2. 第二种定义b变量的方式是通过单引号定义变量。这个方式会 的特点是：输出变量时引号里是什么就输出什么，即使内容中有变量也会把变量原样输出，此方法比较适合定义显示纯字符串。
3. 第三种定义c变量方式是通过双引号定义变量。这个方式的特点是：输出变量时引号里的变量会经过解析后输出该变量内容。而不是把引号中变量名元杨树称呼，适合于字符串中附带有变量的内容的定义。

> 习惯：数字不加引号，其他默认加双引号

#### 定义变量单引号、双引号于不加引号

有关单引号、双引号与不加引号的简要说明如下：

|名称|解释|
|:--|:--|
|`单引号`|单引号内的所有内容都原样输出，或者描述为单引号里面看到的是什么就会输出什么|
|`双引号`|把双引号内的所有内容都输出出来，如果内容中有命令（要反引下）、变量、特殊转移符等，会先把变量、命令、转义符解析出来，然后在输出最终内容来|
|`无引号`|把内容输出出来，会将含有空格的字符串视为一个整体输出出来，如果内容中有命令、变量等，会先把变量、命令解析出结果，然后在输出最终内容来，如果字符串带有空格等特殊字符，则不能完整的输出，需要改加双引号，一般连续的字符串，数字，路径等可以不加任何引号，不过无引号的情况最好用双引号代替之|

- 范例

经过反引号的`date`命令测试

```bash
$ echo 'today is date'
today is date
# 单引号看到什么就输出什么
$ echo 'today is `date`'
today is `date`
# 单引号看到什么就输出什么
$ echo "today is date"
today is date
# 双引号时如果里面是变量，会先把变量解析成具体内容显示
$ echo "today is `date`"
today is Wed Apr  1 15:46:18 CST 2015
# 双引号时如果里面是变量，会先把变量解析成具体内容显示
$ echo "today is $(date)"
today is Wed Apr  1 15:46:24 CST 2015
# 双引号时如果里面是变量，会先把变量解析成具体内容显示
# 对于连续的字符串等内容一般不加引号也可，加双引号一般比较保险，推荐。
```
变量定义后，调用时测试
```bash
$ ANSHENG=testchars		#-->创建一个不带引号的变量
$ echo $ANSHENG			#-->不加引号，显示一个变量解析后的内容
testchars
$ echo '$ANSHENG'		#-->单引号，显示一个变量本身
$OLDBO
$ echo "$ANSHENG"		#-->双引号，显示一个变量内容，引号内可以是变量、字符串等
testchars
$ ETT=123
$ awk 'BEGIN {print "$ETT"}'
$ETT
$ awk "BEGIN {print "$ETT"}"
123
$ awk 'BEGIN {print '$ETT'}'
123
```

#### 自定义普通字符串变量的建议

- 内容时纯数字（不带空格），定义方式可以不加引号（单或双）

```bash
a.ANSHENGAge=33
b.NETWORKING=yes
```

- 没特殊情况，字符串一般用双引号定义，特别是多个字符串中间有空格时

```bash
a.NFSD_MODULE=”no load”
b.MyNAME=”ANSHENG is a handsome boy.”
```
- 变量内容需要原样输出时，要用单引号（``）
例如：
```bash
a.ANSHENG_NAME=’ANSHENG’
```

#### 变量的命名规范

- 变量命名要统一，使用全部大写字母，语句要清晰，能够正确表达变量内容的含义，过长的英文单词可采用前几个自负代替，多个单词连接使用`_`号链接，引用时，最好以`${APACHE_ERR_NUM}`加大括号或`"${APACHE_ERR_NUM}"`外面加双引号方式引用变量。

- 避免无意义字符或数字：例如下面的COUNT，并不知道其确切含义；

范例:COUNT的不确切定义

```bash
CONUT=`grep keywords file`
if [ ${CONUT} -ne 22 ]
then
	echo “Do Something”
fi
```

- 全局变量和局部变量命名

a.脚本中的全局变量定义，如`ANSHENG_HOME`或`ANSHENGHOME`，在变量使用时使用`{ }`将变量括起或`"{ANSHENG_HOME}"`

范例：操作系统函数库脚本内容全局变量截取例子

```bash
$ cat /etc/init.d/functions
```

b.脚本中局部变量定义：存在于脚本函数（function）中的变量称为局部变量，要以local方式进行声明，使只只在本函数作用域内有效，防止变量在函数中的命名与变量外部程序中变量重名造成异常.

范例3:函数中的变量定义

```bash
checkpid() {
  local i

  for i in $* ; do
          [ -d "/proc/$i" ] && return 0
  done
  return 1
}
```

- 变量合并：当某些变量或配置项要组合起来才有意义时，如文件的路径和文件名称，建议将要组合的变量合并到一起赋值给一个新的变量，这样既方便之后的调用，也为以后进行修改提供了方便。

范例：自动化安装httpd的脚本变量合并定义

```bash
VERSION="2.2.22"
SOFTWARE_NAME="httpd"
SOFTWARE_FULLNAME="${SOFTWARE_NAME}-${VERSION}.tar.gz"
```

- 变量定义总结：多学习模仿操作系统自带的`/etc/init.d/function`函数库脚本的定义思路。

1. 变量名只能为字母，数字，下划线，字母开头。
2. 规范的变量名定义方法：见名只意
	1. `ANSHENGAge=1`	每个单词的首字母大写
	2. `ANSHENG_age=1`	单词之间用`"_"`
	3. `ANSHENGAgeSex=1`	驼峰语法:首个单词的首字母小写，其余单词首字母大写
3. `=`号的知识，`a=1`等号是赋值的意思
4. 比较是不是相等，为`==`打印变量，变量名前接$符号，变量名后面紧接着字符的时候，要用大括号括起来
5. 注意变量内容引用方法，一般用引号，简单连续字符串可以不加引号，希望原样输出，使用单引号。
6. 变量内容是命令，这个时候要用反引号或者`$()`把变量括起来使用。

## 特殊变量

### 位置变量

|符号|描述|
|:--|:--|
|`$0`|获取当前执行的shell脚本文件名，包括脚本路径|
|`$n`|获取当前执行的shell脚本的第n个参数值，`n=1..9`，当n为0时表示脚本的文件名，如果n大于9就要用大括号括起来`${10}`|
|`$*`|获取当前的shell的所有参数，将所有的命令行参数视为单个字符串，相当于“$1$2$3$4$5”..注意与$#的区别|
|`$#`|获取当前shell命令行中参数的总个数|
|`$@`|这额程序的所有参数`$1` `$2` `$3` `…`，这是将参数传递给其他程序的最佳方式，因为他会保留内嵌在每个参数里的任意空白|

范例

```bash
$ sh /server/scripts/tel.sh 
/server/scripts/tel.sh
$ cd /server/scripts/
$ sh tel.sh 
tel.sh
$ sh b.sh `seq 1 15`
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
15
$ sh b.sh `seq 1 100`
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
100
$ dirname /server/scripts/tel.sh 
/server/scripts
$ basename /server/scripts/tel.sh 
tel.sh
$ sh /server/scripts/tel.sh 
/server/scripts
tel.sh
$ cat tel.sh 
dirname $0
basename $0
$ echo \${1..15}   
$1 $2 $3 $4 $5 $6 $7 $8 $9 $10 $11 $12 $13 $14 $15
$ cat te3.sh 
echo $1 $2 $3 $4 $5 $6 $7 $8 $9 ${10} ${11} ${12} ${13} ${14} ${15}
$ sh te3.sh {a..z}
a b c d e f g h i j k l m n o
```

判断参数的个数

```bash
$ cat tejing4.sh 
[ $# -ne 2 ] && {
echo "muse two"
exit 1
}
echo $1 $2
第二个脚本
$ cat tejing5.sh 
#no1
if [ $# -ne 2 ]
  then
   echo "USAGE:/bin/sh $0 arg1 arg2"
   exit 1
fi

#no2
echo $1 $2
```

控制用户传参个数

```bash
$ cat a.sh
#!/bin/bash
[ $# -ne 2 ] &&{
  echo "muse two"
  exit 1
}
echo ansheng
$ sh a.sh sa
muse two
$ sh a.sh sa ha
ansheng
```

- $*和$@的区别例子

1. `$*`将所有的命令行所有参数视为单个字符串，等同于`$1$2$3`,`$*`;
2. `$@`将命令行每个参数视为单独的字符串，等同于`$1` `$2` `$3`。这是将参数传递给其他程序的最佳方式，因为他会保留所有内嵌在每个参数里的任何空白。

> 上述区别仅在于双引号的时候，即`$*`和`$@`.

范例

```bash
$ set -- "I am" handsome ansheng.		# 传入三个参数
$ echo $#  # 现在有三个参数
3
$ for i in "$*";do echo $i;done
# 在有双引号的情况下，参数里因好内内容当作一个参数输出了，这才真正符号我们传入的参数需求，set -- "I am" handsome ansheng.
I am handsome ansheng.
$ for i in "$@";do echo $i;done
# 在有双引号的情况下，每个参数独立输出
I am
handsome
ansheng.
$ for i ;do echo $i;done
# 去掉in变量列表，相当于in “$@”
I am
handsome
ansheng.
$ for i in $*;do echo $i;done
# 不加双引号，把所有参数输出，然后第一个参数“I am”也拆开输出了.
$ for i in $@;do echo $i;done
# 不加双引号，把所有参数输出，然后第一个参数“I am”也拆开输出了.
```

### 进程状态变量

|符号|描述|
|:--|:--|
|`$$`|获取当前shell脚本的进程号（PID）|
|`$!`|执行上一个指令的PID|
|`$?`|获取执行上一个指令的返回值（0为成功，非0为失败）|
|`$_`|在此之前执行的命令或脚本的最后一个参数|

通过脚本控制错误返回值

```bash
$ cat fanhui.sh 
exit 100
$ sh fanhui.sh 
$ echo $?
100
```

`$$案例`

```bash
#!/bin/sh
pidpath=/tmp/a.pid
if [ -f "$pidpath" ] 
  then 
      kill -USR2 `cat $pidpath` >/dev/null 2>&1
      rm -f $pidpath
fi
echo $$ >$pidpath
sleep 300
```

`$_`

```bash
$ /etc/init.d/ntpd start
Starting ntpd:                                             [  OK  ]
$ echo $_
start
```

打印字符小于6的行
```bash
$ cat ab.sh 
for n in I am ansheng linux welcome to our training.
do
  if [ ${#n} -lt 6 ]
  then 
   echo $n 
 fi
done
$ sh ab.sh  
I
am
linux
to
our
```
输出整个字符串的一部分
```bash
$ ansheng="I am ansheng"
$ echo ${ansheng:2}
am ansheng
$ echo ${ansheng:2:2}
am
```
输出指定短的字符
```bash
$ echo ${ansheng:2}      
am ansheng
$ echo $ansheng|cut -c 3-
am ansheng
$ echo ${ansheng:2:2}    
am
$ echo $ansheng|cut -c 3-4
am
```
`##`
```bash
$ echo ${ansheng#a*c} 
ABC123ABCabc
$ echo $ansheng
abcABC123ABCabc
$ echo ${ansheng##a*c}
```
`%%`
```bash
$ echo $ansheng       
abcABC123ABCabc
$ echo ${ansheng%a*c} 
abcABC123ABC
$ echo ${ansheng%%a*c}

$ echo ${ansheng%C*bc} 
abcABC123AB
$ echo ${ansheng%%C*bc}
abcAB
```
`/替换`
```bash
$ ansheng=abcABC123ABCabc
$ echo $ansheng          
abcABC123ABCabc
$ echo ${ansheng/abc/ansheng}
anshengABC123ABCabc
$ echo ${ansheng/#abc/ansheng}
anshengABC123ABCabc
$ echo ${ansheng/%abc/ansheng}
abcABC123ABCansheng
```
批量改名
```bash
$ f=stu_102999_5_finished.jpg
$ echo $f
stu_102999_5_finished.jpg
$ echo ${f/_finished//}
stu_102999_5/.jpg
$ echo ${f/_finished/} 
stu_102999_5.jpg
$ f=stu_102999_5_finished.jpg
$ echo ${f/_finished/}       
stu_102999_5.jpg
$ mv $f `echo ${f/_finished/} `
$ ls -lrt|tail -5
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_5.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_4_finished.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_3_finished.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_2_finished.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_1_finished.jpg
$ for f in `ls *fin*.jpg`;do mv $f `echo ${f/_finished/}`;done
$ ls -lrt|tail -5
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_5.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_4.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_3.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_2.jpg
-rw-r--r-- 1 root root   0 4月   2 16:59 stu_102999_1.jpg
```

## 变量的数值（整数）计算

变量的数值计算常见的有如下几个命令：

1. (())
2. let
3. expr
4. bc(小数)\$[]

其他都是整数

- `(())`用法：(此方法很常用)

如果要执行简单的整数运算，只需将特定的算术表达式用” $((“和”))”括起来即可。

shell的算术运算符号常置于” $((“…….”))”的语法中。这一语法如同双引号功能，除了内嵌双引号无需转义。

|运算符|意义|
|:--|:--|
|`++ --`|增加及减少，可前置也可以放在结尾|
|`+ - ！ ~`|一元的正号与符号；逻辑与位的取反|
|`* / %`|乘法、除法、取余|
|`+ -`|加法、减法|
|`< <= > >=`|比较符号|
|`== !=`|相等与不相等|
|`<< >>`|向左位移 向右位移|
|`&`|位的and|
|`^`|位的异或|
|`\|`|位肚饿或|
|`&&`|逻辑的and|
|`\|\|`|逻辑的or|
|`?:`|条件表达式|
|`= += -= *= /= &= ^=/<<= >>=|=`|赋值运算符a+=1,相当于a=a+1|

- ((a=1+2**3-4%3))=1+8-1=8

```bash
$ ((a=1+2**3-4%3))
$ echo $a
8
$ ((a+=1))      
$ echo $a 
9
$ ((a-=1))
$ echo $a 
8
$ ((a--)) 
$ echo $a
7
$ ((a++))
$ echo $a
8
$ echo $((a-=1))
7
$ echo $a
7
$ echo $((a++)) 
7
$ echo $a
8
$ echo $((a++))
8
$ echo $a
9
$ echo $((a--))
9
$ echo $a
8
$ echo $((--a))
7
$ echo $a
7
```

记忆方法：`++`，`--`
变量a在前，表达式的值为a，然后a自增或自减，变量a在符号后，表达式值自增或自减，然后a值自增或自减。

各种`(())`的计算命令行执行的例子：

```bash
$ echo $((100/5))
20
$ echo $((100*5))
500
$ echo $((100+5))
105
$ echo $((100%5))
0
```

1. 提示：表达式不需要$符号，直接`((100 % 5))`,如果要输出，就加$，例如：`echo ((100%5))`
2. `(())`里的所有字符之间

```bash
$ echo $((100**5))
10000000000
$ echo $((100-5)) 
95
```
各种`(())`运算的shell脚本例子：
```bash
$ cat te5.sh 
#!/bin/bash
a=6
b=2
echo "a-b =$(( $a - $b ))"
echo "a+b =$(( $a + $b ))"
echo "a*b =$(( $a * $b ))"
echo "a/b =$(( $a / $b ))"
echo "a**b =$(( $a ** $b ))"
echo "a%b =$(( $a % $b ))"
$ sh te5.sh 
a-b =4
a+b =8
a*b =12
a/b =3
a**b =36
a%b =0
```
a,b两个变量通过命令行脚本传参的方式实现上面混合运算脚本的功能
```bash
$ cat te5.sh 
#!/bin/bash
a=$1
b=$2
echo "a-b =$(( $a - $b ))"
echo "a+b =$(( $a + $b ))"
echo "a*b =$(( $a * $b ))"
echo "a/b =$(( $a / $b ))"
echo "a**b =$(( $a ** $b ))"
echo "a%b =$(( $a % $b ))"
$ sh te5.sh 6 2
a-b =4
a+b =8
a*b =12
a/b =3
a**b =36
a%b =0
```
请实现一个加、减、乘、除功能的计算器
第一种方式：
```bash
$ cat jisuanqi.sh 
#!/bin/bash
echo $(($1$2$3))
```
第二种方式：
```bash
$ cat jisuanqi2.sh
#!/bin/bash
echo  $(($1))
```
第三种方式：
```bash
$ cat jisuanqi3.sh 
echo "$1$2$3"|bc
```

### let命令的用法

- 语法

```bash
let 赋值表达式
```

> let赋值表达式功能等同于：((赋值方式))

- 范例1:给变量i加8

```bash
$ i=1
$ let i=8+i
$ echo $i
9
$ i=i+8	-去掉let定义
$ echo $i
# i+8 = 输出的结果
```

> let i=i+8等同于((i=i+8))，但后者效率更高

### expr命令用法

expr命令是一般用于整数值，但也可以用于字符串，用来表达变量的值，同时expr是一个手工命令行计算器。

语法

```bash
expr Expression
```

```bash
$ expr 2 + 2
4
$ expr 4 - 2
2
$ expr 2 \* 2
4
$ expr 4 / 2		除法
2
$ expr 4 % 2		取余数
0
```

1. 注意：运算符及计算的数字左右都有至少一个空格
2. 使用乘号时，必须用反斜线屏蔽其特定含义。因为shell可能会误解星号的含义

- 增量计数

expr在循环中可用于增量计算。首先，循环初始化为0，然后循环值加1，反引号的用法为命令替代，最基本的一种是从（expr）命令接受输出并将之放入循环变量
```bash
$ i=0
$ i=`expr $i + 1`
$ echo $i
1
```
expr $[$a+$b]表达式形式，其中$a $b可为整数值。
```bash
$ expr $[2+5]
7
$ expr $[2*5]
10
$ expr $[2**5]
32
$ expr $[2/5]

```

expr判断文件或字符串的扩展名小案例： 

```bash
$ cat expr1.sh 
#!/bin/sh
if expr "$1" : ".*\.pub" &>/dev/null
  then 
   echo "you are using $1"
else
   echo "pls use *.pub file"
fi
```

expr判断变量是否为整数

```bash
$ cat expr2.sh 
#!/bin/bash
expr $1 + 1 >/dev/null 2>&1
if [ $? -eq 0 ]
then
   echo int
else
   echo "fei int"
fi
```
通过expr计算字符串的长度
```bash
$ chars=`seq -s" " 100`
$ echo ${#chars}
291
$ echo $(expr length "$chars")
291
```
计算效率
```bash
[root@root]# time for i in $(seq 11111);do count=${#chars};done;

real    0m0.525s
user    0m0.523s
sys     0m0.001s
[root@root ]# time for i in $(seq 11111);do count=`echo ${chars}|wc -L`;done;
 
real    0m16.320s
user    0m0.340s
sys     0m1.106s
$ time for i in $(seq 11111);do count=`echo expr length "${chars}"`;done; 

real    0m4.700s
user    0m0.311s
sys     0m1.040s
```

### bc命令的用法（可以整数也可以小数）

bc是UNIX下的计算器，它也可以用在命令行下面：
例：给变量i加1
```bash
i=2
i=`echo $i+1|bc`
```
因为bc支持科学计算，所以这种方法功能非常强发
特点：bc的独有特点是支持小数运算，当然也支持整数运算。

```bash
$ i=2
$ i=`echo $i+1|bc`
$ echo $i
3
$ echo "obase=2;8"|bc	# 十进制8转换为二进制
1000
```

范例子：通过命令输出1+2+3+4+5+6+7+8+9+10的表达式，并计算出结果
输出内容如：1+2+3+4+5+6+7+8+9+10=55
```bash
$ echo "`seq -s '+' 10`="$((`seq -s "+" 10`)) 
1+2+3+4+5+6+7+8+9+10=55
$ echo `seq -s '+' 10`=`seq -s "+" 10|bc`          
1+2+3+4+5+6+7+8+9+10=55
$ echo `seq -s '+' 10`=`seq -s " + " 10|xargs expr`
1+2+3+4+5+6+7+8+9+10=55
$ echo {1..9}"+" 10 =`echo {1..9}"+" 10|bc`|sed 's# ##g' 
1+2+3+4+5+6+7+8+9+10=55
```

### typeset命令的用法

使用整数变量直接进行计算
例如
```bash
$ typeset -i A=1 B=3
$ A=A+B
$ echo $A
4
```

### $[]的用法

```bash
实践操作演示
$ echo $[ 2 + 3 ]
5
$ echo $[ 3-2 ]
1
$ echo $[ 3*2 ]
6
```

## 变量的输入

Shell变量除了可以直接赋值或脚本传参外，还可以使用read命令从标准输入获得，read为内置命令。
【语法格式】
	read [常用参数] [变量名]
【常用参数】
	-p prompt:设置提示信息
	-t timeout:设置输入等待的时间，单位默认为秒
范例1：read的基本读入
```bash
$ read -p "pls input a num:" a1 a2 -要有空格
pls input a num:12 13
$ echo $a $b
1 2
```

通过read读入变量

```bash
$ cat te5.sh 
#!/bin/bash
read -p "int :" a b
echo "a-b =$(( $a - $b ))"
echo "a+b =$(( $a + $b ))"
echo "a*b =$(( $a * $b ))"
echo "a/b =$(( $a / $b ))"
echo "a**b =$(( $a ** $b ))"
echo "a%b =$(( $a % $b ))"
```

两种方法实现：
1. read
2. 传参
需要判断传参（变量）个数，及是否为数字

### 制作一个简单的计算器

```bash
$ cat tete5.sh 
#!/bin/bash
#read -p "pls input two num:" a b
a=$1
b=$2
#no1..
expr $a + 1 &>/dev/null
[ $? -ne 0 ] &&{
 echo a failure
 exit 1
}
#no2
expr $b + 1 &>/dev/null
[ $? -ne 0 ] &&{
 echo b failure 
 exit 1 
}
#no3..
[ -z "$a" -o -z "$b" ] &&{
echo "bu neng wei kong"
exit 1
}
#no4..
[ $# -ne 2 ] &&{
echo "can shu guo duo"
exit 1
}
echo "a-b =$(( $a - $b ))"
echo "a+b =$(( $a + $b ))"
echo "a*b =$(( $a * $b ))"
echo "a/b =$(( $a / $b ))"
echo "a**b =$(( $a ** $b ))"
echo "a%b =$(( $a % $b ))"
$ cat te5.sh 
#!/bin/bash
read -p "pls input two num:" a b
#no1..
expr $a + 1 &>/dev/null
[ $? -ne 0 ] &&{
 echo failure
 exit 1
}
#no2
expr $b + 1 &>/dev/null
[ $? -ne 0 ] &&{
 echo failure 
 exit 1 
}
#no3..
[ -z "$a" -o -z "$b" ] &&{
echo "bu neng wei kong"
exit 1
}
#no4..
[ $# -ne 2 ] &&{
echo "can shu guo duo"
exit 1
}
echo "a-b =$(( $a - $b ))"
echo "a+b =$(( $a + $b ))"
echo "a*b =$(( $a * $b ))"
echo "a/b =$(( $a / $b ))"
echo "a**b =$(( $a ** $b ))"
echo "a%b =$(( $a % $b ))"

$ cat read6.sh 
#!/bin/bash
read -p "Pls input two num:" a b

#no.1
#[ ${#a} -eq 0 -o  ${#b} -eq 0 ]
[ -z "$a" -o -z "$b" ] &&{
 echo "the args cat not be null."
 exit 1
}

#[ $# -ne 2 ]&&{
# echo "the args cat not be null."
# exit 1
#}

#no.2
expr $a + $b &>/dev/null
[ $? -ne 0 ] &&{ 
 echo "both args must be int."
 exit 1
}
#no.3
echo "a-b =$(( $a - $b ))"
echo "a+b =$(( $a + $b ))"
echo "a*b =$(( $a * $b ))"
echo "a/b =$(( $a / $b ))"
echo "a**b =$(( $a ** $b ))"
echo "a%b =$(( $a % $b ))"
```
判断呢输入的参数是否为0
```bash
LIN=$(expr $a + $a)
[ "$LIN" -eq "$a" ] && {
  echo "a bu neng wei 0"
  exit 4
}
LIN1=$(expr $b + $b)
[ "$LIN1" -eq "$b" ] && {
  echo "b bu neng wei 0"
  exit 4
}
```

### 制作一个简单的菜单

```bash
$ cat caidan.sh
#!/bin/bash
menu(){
cat <<END
    1.[install lamp]
    2.[install lnmp]
    3.[exit]
    pls input the num you want:	
END
}
menu
read -t 15 a
#!=0
[ $a -eq 0 ] &&{
  echo "pls input right num."
  exit 1
}
#=1 2 3
[ $a -eq 1 -o $a -eq 2 -o $a -eq 3 ] ||{
  echo "pls input right num."
  exit 1
}
#=1
[ $a -eq 1 ] &&{
  echo "installing lamp"
  sleep 3
  echo "lamp is installed."
  exit
}
#2
[ $a -eq 2 ] &&{
  echo "installing lnmp"
  sleep 3
  echo "lnmp is installed."
  exit
}
#3
[ $a -eq 3 ] &&{
  exit
}
```