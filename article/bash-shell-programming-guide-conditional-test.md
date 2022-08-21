# Bash Shell编程指南之条件测试

- 条件测试语法

在bash的各种流程控制结构中通常要进行各种测试，然后根据测试结果执行不同的操作，有时也会通过与`if`等条件语句相结合，让我们可以方便的完成判断。

 语法说明

```
格式1:	test<测试表达式>
格式2:	[ <测试表达式> ]
格式3:	[[ <测试表达式> ]]
```
> 格式1和格式2是等价的，格式3为扩展的test命令

在`[[]]`中可以使用通配符进行匹配。`&&`、`||`、`>`、`<`等操作符可以应用与`[[]]`中，但不能应用与`[]`中。

对整数进行关系型运算，也可以使用Shell的算术运算符`(())`。

- 语法例子

```
格式1：test<测试表达式>
```

范例1：test测试一个文件存不存在

```bash
$ test -f file && echo 1 || echo 0
0
$ touch file
$ test -f file && echo 1 || echo 0
1
```

范例2：test命令非`(!)`的写法

```bash
$ touch ! -f file && echo 0 || echo 1
0
$ arg=shell
$ test -n "$arg" &&echo 1||echo 0
1
$ test ! -n "$arg" &&echo 1||echo 0
0
```

`-z`如果为0代表真

```bash
$ test -z "$arg" && echo 1 || echo 0
0
```

- 格式2：	[ <测试表达式> ]

范例：`[  ]`

```bash
$ [ -f file ]&& echo 1||echo 0
0
$ touch file
$ [ -f file ]&& echo 1||echo 0
1
```

如果存在就返回0

```
$ [ ! -f file ]&& echo 1||echo 0
0
$ ll file 
-rw-r--r-- 1 root root 0 Apr  4 15:41 file
```

- 格式3：`[[ <测试表达式> ]]`

范例：`[[  ]]`

```bash
#如果file文件存在就返回1，不存在就返回0
$ [[ -f file ]] && echo 1 || echo 0
0

##如果file文件不存在就返回1
$ [[ ! -f file ]] && echo 1|| echo 0
1

#如果file文件和dir目录都存在的时候就返回1
$ [[  -f file && -d dir ]] && echo 1 || echo 0
0
$ touch file
$ mkdir dir
$ [[  -f file && -d dir ]] && echo 1 || echo 0
1

#当file文件或者dir目录存在的时候就返回1
$ [[  -f file || -d dir ]] && echo 1 || echo 0
1
$ rm -f file 
$ [[  -f file || -d dir ]] && echo 1 || echo 0
1
$ rm -fr dir/
$ [[  -f file || -d dir ]] && echo 1 || echo 0
0
```

> 上述三种判断中，参数都是一样的，只是语法不一样

### 文件测试操作符

常用文件测试操作符

|符号|原文|译文|
|:--|:--|:--|
|`-f 文件 file`|FILE exists and is a regular file|若文件存在且为普通文件则真|
|`-d 文件 directory`|FILE exists and is a directory|若文件存在且为目录文件则真|
|`-s 文件 size`|FILE exists and has a size greater than zero|若文件存在且为不为空（文件大于非0）则真|
|`-e 文件 exist`|FILE exists|若文件存在并且是普通文件则真，要区别-f|
|`-r 文件 read`|FILE exists and read permission is granted|若文件存在且可读为真|
|`-w 文件 write`|FILE exists and write permission is granted|若文件存在且可写为真|
|`-x 文件 excute`|FILE exists and execute (or search) permission is granted|若文件存在且可执行为真|
|`-L 文件 Link`|FILE exists and is a symbolic link (same as -h)|若文件存在且为链接文件则真|
|`f1 -nt f2 newer than`|FILE1 is newer (modification date) than FILE2|若文件f1比文件f2新则真|
|`f1 -ot f2 older than`|FILE1 is older than FILE2|若文件f1比文件f2旧则真|

#### 范例

- `-f`或者`-e`

```bash
#如果文件存在就返回1，不存在就返回0
$ touch file
$ [ -f file ] && echo 1 || echo 0
1
$ rm -f file 
$ [ -f file ] && echo 1 || echo 0
0
-----------------------------------------------------------------------------------
$ [ -e file ] && echo 1 || echo 0
0
$ touch file
$ [ -e file ] && echo 1 || echo 0
1
```

`-d`

```bash
#如果目录存在就返回1，不存在就返回0
$ mkdir dir
$ [ -d  dir ]&& echo 1|| echo 0
1
$ rm -fr dir/
$ [ -d  dir ]&& echo 1|| echo 0
0
$ touch file
$ chmod 000 file
#如果有读权限则返回1，没有返回0
$ [ -r file ]&&echo 1 ||echo 0
0
$ chmod u+r file 
$ [ -r file ]&&echo 1 ||echo 0
1
#如果有写权限则返回1，没有返回0
$ [ -w file ]&&echo 1 ||echo 0
0
$ chmod u+w file 
$ [ -w file ]&&echo 1 ||echo 0
1
#如果有执行权限则返回1，没有返回0
$ [ -x file ]&&echo 1 ||echo 0
0
$ chmod u+x file 
$ [ -x file ]&&echo 1 ||echo 0
1
```

### 字符串测试操作符

字符串测试操作符的作用：比较两个字符串是否相同、字符串长度是否为0，字符串是否为NULL等。

`=`比较两个字符串是否相同，与`==`等价，如`if[ "$a" = "$b" ]`，其中`"$a"`这样的变量最好用`""`括起来，因为如果有中间有空格，`*`等符号就可能出错了，当然更好的方法就是`[ "${a}" = "${b}" ]`。`"！="`比较两个字符串是否相同，不同则为`"是"`。

|常用字符串测试操作符|说明|
|:--|:--|
|`-z "字符串"`|若串长度为0则真，-z可以理解为zero|
|`-n "字符串"`|若串长度不为0则真，-n可以理解为no zero|
|`"串1"="串2"`|若串1等于串2则真，可以使用"=="代替"="|
|`"串1"！="串2"`|若串1不等于串2则真，但不能用"!==" 代替"!="|

> 以上表格中的字符串测试操作符号务必要用""引起来。

- 字符串测试操作符提示

1. `-n`比较字符长度是否不为零，如果不为零则真，如：`[ -n "$myvar" ]`
2. `-z`比较字符串长度是否为零，如果等于零则为真，如：`[ -z "$myvar" ]`
特别注意：对于以上表格中的字符串测试操作符号，如`[ -n "$myvar" ]`，要把字符串用`"引起来"`
3. 字符串比较，比较符号两端最好都有空格，多参考系统脚本。

- 注意事项

1.  字符串或字符串变量比较都要加双引号在比较，
2.  字符串或字符串变量比较，比较符号两端最好都有空格，参考系统脚本。

```bash
$ arg="ansheng"
$ echo $arg
ansheng
$ [ -z "$arg" ]&&echo 1||echo 0
0
$ arg=""
$ echo $arg

$ [ -z "$arg" ]&&echo 1||echo 0
1
$ [ "ddd" = "fff" ]&&echo 1||echo 0
0
$ [ "ddd" = "ddd" ]&&echo 1||echo 0
1
```

###  整数二元比较操作符

|在[]中使用比较符|在(())和[[]]中使用的比较符|说明|
|:--|:--|:--|
|`-eq`|`==`|equal的缩写，相等|
|`-ne`|`!=`|not equal的缩写，不相等|
|`-gt`|`>`|大于greater equal|
|`-ge`|`>=`|小于等于greaer equal|
|`-lt`|`<`|小于类似less than|
|`-le`|`<=`|小于等于less equal|

- 提示

1. `"<"`符号意思是小于，`if [[ "$a" < "$b" ]],if[ "$a" \< "$b" ]`，在单[]中需要转义，因为shell也用<和>重定向
2. `">"`符号意思是大于，`if [[ "$a" > "$b" ]],if[ "$a" \> "$b" ]`在单[]中需要转义，因为shell也用<和>重定向
3. `"="`符号意思是等于，`if [[ "$a" = "$b" ]] if["$a"="$b"]` 在单[]中不需要转义

- 特别提示

经过实践，`"="`和`"!="`在`[]`中使用不需要转义，包含`">"`和`"<"`的符号在[]中使用转义，对于数字不转义的结果未必会报错，但是结果可能不对。

- 测试结果

1. 整数加双引号是对的。
2. `[[]]用`-eq`等的写法是对的。
3. `[]`用`>`写语法没错，结果不对。

- 范例

开发shell脚本分别实现以定义变量，脚本传参以及read读入的方式比较2个整数大小。用条件表达式（禁止if）进行判断并以屏幕输出方式提醒用户比较结果。注意：一共是开发3个脚本。当用脚本传参以及read读入的方式需要对变量是否为数字、并且传参是否为2做判断。

```bash
$ cat panduan.sh
#!/bin/bash
read -p "liang ge zheng shu:" a b
[ -z "$a" -o -z "$b" ]&& {
 echo "qin shu ru liang ge can shu." 
 exit 1
}
expr "$a" + 1 >/dev/null >/dev/null 2>&1
[ "$?" -ne 0 ]&&echo "a bi xu shi zheng shu."&&exit 1

expr "$b" + 1 >/dev/null 2>&1
[ "$?" -ne 0 ]&&echo "b bi xu shi zheng shu."&&exit 1

[ "$a" -gt "$b" ]&&echo "$a > $b"
[ "$a" -lt "$b" ]&&echo "$a < $b"
[ "$a" -eq "$b" ]&&echo "$a = $b"
```

```bash
$ cat int_cpm_01.sh 
#!/bin/bash
read -p "Pls input two num:" a b

#no.1
[ -z "$a" -o -z "$b" ] &&{
 echo "the args cat not be null."
 exit 1
}

#no.2
expr $a + $b &>/dev/null
[ $? -ne 0 ] &&{ 
 echo "both args must be int."
 exit 1
}
#no.3
[ $a -gt $b ]&&{
  echo "$a > $b"
  exit 0
}

[ $a -eq $b ]&&{
  echo "$a = $b"
  exit 0
}
[ $a -lt $b ]&&{
  echo "$a < $b"
  exit 0
}
echo ansheng
```
- 范例

1. 当用户输入1时，执行`/server/scripts/lamp.sh`，脚本内容输出`"installing lamp"`后退出脚本；当用户输入2时，执行`/server/scripts/lnmp.sh`输出`"installing lnmp"`后退出脚本，当输入3时，退出当前菜单及脚本；当输入任何其它字符，给出提示`“Input error”`后退出脚本。
2. 要对执行的脚本进行相关条件判断，例如：脚本是否存在，是否可执行等。

```bash
$ cat caidan.sh 
#!/bin/bash
cat <<EOF
  1.[install lamp]
  2.[install lnmp]
  3.exit
EOF
read -p "Pls input the num you want:" a
[ "$a" -eq 1 -o "$a" -eq 2 -o "$a" -eq 3 ]>/dev/null 2>&1||{
 echo "Input error"
 exit 1
}
PATH="/server/scripts"
LAMP="lamp.sh"
LNMP="lnmp.sh"
#Option 1
[ "$a" -eq 1 ]&&{
  [ -d $PATH ]||{
   echo "not dir"
   exit 1
 }
  [ -f $PATH/$LAMP ]||{
    echo "not file."
    exit 1
 }
  [ -x $PATH/$LAMP ]||{
    echo "error: $PATH/$LAMP not execute permission"
    exit 1
 }
  /bin/sh $PATH/$LAMP
  exit 0
}
#Option 2
[ "$a" -eq 2 ]&&{
  [ -d $PATH ]||{
   echo "not dir"
   exit 1
 }
  [ -f $PATH/$LNMP ]||{
    echo "not file."
    exit 1
 }
  [ -x $PATH/$LNMP ]||{
   echo "error: $PATH/$LNMP not execute permission"
   exit 1
 }
  /bin/sh $PATH/$LNMP
  exit 0
}
#Option 3
[ "$a" -eq 3 ]&&{
  exit 0
}
```

- 多级菜单

```bash
$ cat menu.sh 
#!/bin/bash
while true
do
mun(){
cat <<EOF
  1.[install lamp]
  2.[install lnmp]
  3.exit
EOF
read -p "Pls input the num you want:" a
}
mun
[ "$a" -eq 1 -o "$a" -eq 2 -o "$a" -eq 3 ]>/dev/null 2>&1||{
 echo "Input error"
 sleep 2
 clear
 continue
}
#Option 1
[ "$a" -eq 1 ]&&{
  /bin/sh /server/scripts/lamp.sh
  sleep 2
  clear
  continue
}
#Option 2
[ "$a" -eq 2 ]&&{
  /bin/sh /server/scripts/lnmp.sh
  sleep 2
  clear
  continue
}
#Option 3
[ "$a" -eq 3 ]&&{
  exit 0
}
done

$ cat menu01.sh   
#!/bin/sh
while true
do
path=/server/scripts
[ ! -d "$path" ] && exit 1

#menu
menu(){
cat <<END
=================================
    1.[install lamp]
    2.[install lnmp]
    3.[exit]
    pls input the num you want:
=================================
END
read num
}
menu
expr $num + 1 &>/dev/null
[ $? -ne 0 ]&&{
 echo "the num you input must be {1|2|3}"
 sleep 2
 clear
 continue
}

[ $num -eq 1 ]&&{
   echo "start installing lamp."
   sleep 2;
   [ -x "$path/lamp.sh" ]||{
    echo "$path/lamp.sh does not exist or not exec."
    exit 1
   }
   $path/lamp.sh
   sleep 2
   clear
   continue
}
[ $num -eq 2 ]&&{
   echo "start installing LNMP."
   sleep 2;
   [ -x "$path/lnmp.sh" ]||{
    echo "$path/lnmp.sh does not exist or not exec."
    exit 1
   }
   $path/lnmp.sh
   sleep 2
   clear
   continue
}
[ $num -eq 3 ] && exit 0
[ $num -ne 1 -a $num -ne 2 -a  $num -ne 3 ] &&{
 echo "Input ERROR"
  sleep 2
  clear
  continue
}
done
#continue重新从头开始执行
```

## 逻辑操作符

|在[ ]中使用的逻辑操作符|在[[ ]]中使用的逻辑操作符|说明|
|:--|:--|:--|
|`-a`|`&&`|and与，两端都为真，则真|
|`-o`|`||`|or或，两端有一个则真|
|`!`|`!`|not非，相反则真|

- 提示

1. `!`中文意思是反：与一个逻辑值相反的逻辑值
2. `-a`中文意思是与`（and &&）`：两个逻辑值都为"真"返回值才为"真"，反之为"假"
3. `-o`中问意思是或`（or||）`：两个逻辑值只要有一个为"真"，返回值为"真"

### 范例

```bash
$ f1=/etc/rc.local
$ f2=/etc/services
$ [[ -f "$f1" && -f "$f2" ]]&&echo 1 ||echo 0
1
$ [ -f "$f1" -a -f "$f2" ]&&echo 1 ||echo 0
1
```
字符串测试
```bash
$ [ -n "$f1" -a -z "$f2" ]&&echo 1 ||echo 0
0
$ [ -n "$f1" -o "$f1" =  "$f2" ]&&echo 1 ||echo 0
1
$ [ -n "$f1" -a "${#f1}" =  "${#f2}" ]&&echo 1 ||echo 0
1
```
整数比较
```bash
$ n1=12;n2=13
$ [[ $n1 -eq $n2 && -z "$n1" ]]&&echo 1||echo 0
0
$ [[ $n1 == $n2 && -z "$n1" ]]&&echo 1||echo 0
0
$ [[ $n1 -eq $n1 && -z "$n1" ]]&&echo 1||echo 0
0
$ [[ ! $n1 -eq $n2 && ! -z "$n1" ]]&&echo 1||echo 0
1
```
输入一个字符，如果等于1 就打印1 如果等于2 就打印2.如果不等于 也不能于2 就退出
```bash
$ cat b.sh
#!/bin/bash
read -p "Error: Please enter 1 or 2 :" a
[ "$a" -eq 1 -a "$a" -eq 2 ]&&{
  echo "Error: Please enter 1 or 2"
  exit 1
}
[ "$a" -eq 1 ]&&{
  echo "1"
  exit 0
}
[ "$a" -eq 2 ]&&{
  echo "2"
  exit 0
}
```
方法1
```bash
$ cat luoji.sh
#!/bin/sh
echo -n "pls input a char:" 
read var
[ "$var" == "1" ]&&{
 echo 1
 exit 0
}
[ "$var" == "2" ]&&{
 echo 2
 exit 0
}

[ "$var" != "2" -a "$var" != "1" ]&&{
 echo error
 exit 0
}
```

> 逻辑操作符使用[]中用-a,-o,!