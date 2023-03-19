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
lsof -nP -iTCP -sTCP:"established" -a -c "WeChat" # '-a'，and; '-t'，可以打印进程号
```