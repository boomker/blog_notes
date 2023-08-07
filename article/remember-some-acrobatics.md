## 奇技淫巧(持续更新)

### 一. 包管理更新提速
#### 1. npm/cnpm 镜像源
```bash
# 安装包时指定源
npm install -gd package --registry=http://registry.npm.taobao.org
# 设置全局npm源码
npm config set registry http://registry.npm.taobao.org

# 永久生效在`~/.npmrc`里添加下面这行
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```

#### 2. Pip 更新所有包
```bash
pip install -U $(pip freeze | awk '{split($0, a, "=="); print a[1]}') # 项目内依赖升级
pip3 list --outdated |awk 'NR>2{print $1}' |xargs -I {} pip3 install --upgrade {} # 全局更新pip包
```

#### 3. Brew 更新所有包
```bash
brew outdated |awk '$0 !~ /pin/{print $1}' |xargs -P 0 brew upgrade 
# `-P`: 在多核 CPU 上并行，参数`0`表示所有核心
```

### 二. Git 相关命令杂技
#### 1. 批量 cherry-pick
```bash
git log main --oneline -18 |grep -v "lockfile" |COL1 |tac |xargs git cherry-pick -x -m 1
# COL1:
	`alias -g COL1="awk '{ print \$1 }'"`
# `-x`: append commit name
# `-m 1`: 遇到Meger 分支，选择 MainLine 分支的 commit 来应用
# -----
glp main  --since=16.days --grep="lockfile" --invert-grep |C1 |tac |X git cherry-pick -m 1 -x
# glp :
	`alias glp="git log --pretty=format:'%Cred%h%Creset - %s %Cgreen(%cr) %C(bold blue)%an%Creset %C(yellow)%d%Creset' " `
# C1:
	`alias -g C1="|choose 0"`
```

### 三. 网络相关命令行杂技
#### 1. 查看本机外网 IP 和地理位置
```bash
curl -L ip.tool.lu
```

#### 2. 查看本机指定状态连接状态
```bash
lsof -nP -i # 列出TCP和UDP连接，并抑制服务名称和端口名称解析
lsof -nP -iTCP -sTCP:"established" # 指定连接状态
# 还可以进一步列出指定应用程序名称的或者PID
lsof -a -d cwd -p processid
lsof -nP -iTCP -sTCP:"established" -a -c "WeChat" # '-a'，and; '-t'，可以打印进程号
```

#### 3. Linux 系统设定 IP 地址
```bash
nmcli con add type ethernet con-name ens33 ifname ens33 ip4 192.168.0.21/24 gw4 192.168.0.1
```

###  四. 文件字符串处理 
#### 0. 同文件不同列截取部分拼接一起输出
```bash
awk -F'[ \t[]+' '{if(NR<13){print $0}else{x=substr($3,1,1);y=substr($5,1,1);print $1"\t",$2,$4"["y x"\t",$NF}}' flypy_twords_fzm.dict.yaml > flypy_twords_fzmx.dict.yaml
```

#### 1. 同文件不同列合并输出
```bash
parallel  --no-run-if-empty  --xapply echo {1}: {2} ::: $(awk -F'[:,]+' '{print $4}' char_common_base.json) ::: "$(awk -F'[][]+' '{print $2}' char_common_base.json)" |tee > hzpy.txt
# xapply: 对输入源进行一一对应排列， 输入源整体加上双引号视为整体
```

#### 2. 同字段值的列去重
```bash
echo '"嚷":  "rǎng", "rāng" \n "嚼":  "jiáo", "jué", "jiào" \n "颤":  "chàn", "zhàn"' |sed 'y/íǐáàǎāé/iiaaaae/' |awk -F'[:,]+' -vORS=" "   '{for(i=1;i<=NF;i++)s[gensub(" ", "", "g",$i),NR]++}{for(j in s){split(j,b,SUBSEP);if(b[2]==NR)print b[1]}printf "\n"}'
# 数组 key 必须携带行号NR， 不然多个字段会被视为多行； 
# 指定输出记录分隔符为空格，避免每个字段都以每行输出
```

#### 3. 同字段值的行提取出来放一起
```bash
awk '{s[$1]++}END{for(i in s){if(s[i]==2){print i |"xargs -I% rg % fcfsu.txt"}}}' fcfsu 
awk -F'\t' '{s[$1]++;w[$0]}END{for(i in w){split(i, a, "\t");if(s[a[1]]>1)print i}}' flypy_wordsu
```

#### 4. 不同行数据，它们同一列中字段值有相同字串，提取这些行放一起
```bash
awk '{s[$1,substr($2,3,4)]++}END{for(i in s){if(s[i]==2){split(i,b,SUBSEP) ;print b[1] |"xargs -I% rg % dyzx"}}}' dyzy 
```

#### 5. a 文件字段值不存在于 b 文件中，打印出来
```bash
awk 'NR==FNR{s[$1]++}{if(NR!=FNR && $0 !~ /^$/){a[$1]++}}END{for(i in s)if(a[i]<1)print i,s[i]}' a b
```

#### 6. 已知某列字符串，批量替换另外一列
```bash
choose -i a 0 |xargs -I % gsed -i -r 's/^%(\t.*)\t([0-9 ]+)$/%\1\t1/g' cn_dicts/flypy_twords.dict.yaml
```

#### 7. 相邻接的奇偶行，同列比大小，取小打印
```bash
awk '{s[NR]=$0}END{for(i in s){if(i%2==0){split(s[i],a); split(s[i-1],b);if(a[2]<b[2]) print s[i];else print s[i-1] }}}' sf
# 数组 idx 从 1 开始计数
```

#### 8. 找出某几列相同的行
```bash
awk '{s[$1$2$3]++}END{for(i in s)if(s[i]>1)print substr(i,1,2)}' a |xargs -I % rg % a.bak > dupa
```

#### 9. 字段长度不合规定的行搂出来
```bash
awk -F'\t' '{if(length(gensub(" ","","g", $2))%2!=0)print $0}' cn_dicts/flypy_super_ext.dict.yaml 
```

#### 10. 精准替换指定位置的字符串
```bash
awk -F'\t'  '{split($1, arr, 'x');split($2, s, " ");{for(i in arr){if(arr[i]=="价" && s[i]=="jp")print $0}}}' wer
awk -F'\t'  '{x=index($1, "系");split($2, a, " ");{if(a[x]=="ji"){a[x]="xi";zm=""}};{for(j in a){zm=zm?zm" "a[j]:a[j]}}{print $1"\t"zm"\t"$NF}}' jkl >jjj
parallel  --no-run-if-empty --xapply sd {1} {2} jkl ::: "$(awk -F'\t' '{print $0}' jkl)" ::: "$(awk -F'\t' '{print $0}' jjj)"
```

### 五.  Bash  技巧
#### 1.  双循环
```bash
for ((i = 0, p = $(pgrep named); i < 12; i ++, p++)); do taskset -pc $i $p;done
```

