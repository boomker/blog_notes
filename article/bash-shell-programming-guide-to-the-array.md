# Bash Shell编程指南之数组

数组是相同类型的元素按一定的顺序排列的集合。

数组把有限个类型相同的变量用一个名字命名，然后用编号区分他们的变化，编号被称为数组的下表，组成数组的各个变量成为数组的元素。

- 创建数组

```bash
$ array=(1 2 3)
```

- 获取数组的长度

```bash
$ echo ${#array[@]}
3
$ echo ${#array[*]}
3
```

- 输出数组

```bash
$ echo ${array[0]}
1
$ echo ${array[1]}
2
$ echo ${array[2]}
3
$ echo ${array[@]}  # 输出数组的所有元素
1 2 3
```

- 数组赋值

通过`数组明[下标]`就可以对其进行引用赋值，如果下标不存在，自动添加新一个数组元素如果存在就覆盖原来的值

```bash
$ array[3]=4
$ echo ${array[@]}
1 2 3 4
$ array[0]=as
$ echo ${array[@]}
as 2 3 4
```

- 删除数组

通过`unset 数组明[下标]`可以清除相应的元素，不带下标清楚整个数据

```bash
$ echo ${array[@]}
as 2 3 4
$ unset array
$ echo ${array[@]}

$ array=(1 2 3)
$ unset array[0]
$ echo ${array[@]}
2 3
```

- 数组内的截断和替换

截取

```bash
$ array=(1 2 3 4 5)
$ echo ${array[@]:1:3}
2 3 4
$ echo ${array[@]:3:2}
4 5
```

替换

```bash
$ echo ${array[@]/5/6}  # 把数组中的5替换成6，原数组不更新
1 2 3 4 6
```

删除

```bash
$ array=(one two three fou five)
$ echo ${array[@]}
one two three fou five
$ echo ${array[@]#o}  # 左边开始最短的匹配
ne two three fou five
$ echo ${array[@]#fo}
one two three u five
$ echo ${array[@]%t**e}
one two fou five
$ echo ${array[@]%%t*e}
one two fou five
```

- 小结

1. 定义
    1. 静态数组：`array=(1 2 3)`
    2. 动态数组：`array=($(ls))`
2. 输出
    1. `${array[@]}或${array[*]}`  输出所有元素
    2. `${#array[@]}或${#array[*]}`  输出数组的长度
    3. `${array[i]}`  输出单个元素，i是数组下标

- 范例

输出元组所有元素

```bash
#!/bin/bash

array=(python bash linux)
for((i=0;i<${#array[*]};i++))
do
  echo ${array[i]}
done
```

每隔10秒检测这些地址是否正常：

```
http://www.baidu.org
http://www.jd.com
http://www.taobao.com
```

```bash
#!/bin/bash

URL=(
http://www.baidu.org
http://www.jd.com
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
or
```bash
#!/bin/bash
. /etc/init.d/functions

url_list=(
http://www.baidu.org
http://www.jd.com
http://www.taobao.com
)
while true
do
check_url(){
  wget -q -o /dev/null $1 &>/dev/null
  if [ $? -eq 0 ]
    then
       action "$1 is ok" /bin/true
  else
       action "$1 is no" /bin/false
  fi
}
for ((i=0;i<${#url_list[@]};i++))
do
     check_url ${url_list[i]}
done
echo -n "wait 10s"
for j in `seq 10`
do
   sleep 1
   echo -n .
   [ $j -eq 10 ] &&  echo 
   
done
done
```