### 一. PDM 包/依赖管理新贵

#### 1. 安装
```bash
pipx install pdm
```
> 画外音：`pipx` 这货没有 `info, home` 子命令

#### 2. 基本配置
```bash
pdm config pypi.verify_ssl false # 非常重要， 不然后续无法装包，报错关键字`trusted_hosts`
pdm config --local pypi.verify_ssl false
pdm config pypi.url http://mirrors.aliyun.com/pypi/simple/
```

####  3. 使用技巧
```bash
pdm init # 交互式初始化项目，会自动创建虚拟环境，解析导入依赖(从 requirements.txt)
pdm lock # 将包依赖写入`pdm.lock`
pdm update # 安装依赖包(在依赖已经写入 lock 时)
# 更新所有的 dev 依赖  
pdm update -d  
# 更新 dev 依赖下某个分组的某个包  
pdm update -dG test pytest
pdm add requests # 新增安装包
pdm import -f requirements requirements.txt # 手动导入依赖包
pdm list # 列出当前项目的依赖包
pdm info # 列出当前项目的基础信息
pdm show djangorestframework # 查看某个包的某体详情
pdm run python manage.py makemigrations # 终端运行Python文件、Django 项目
eval $(pdm venv activate in-project) # 手动激活虚拟环境
```

#### 4. 高级技巧
* **命令别名**
> pyproject.toml 添加 `[tool.pdm.scripts]`
![pdm alias](images/2023/pdm_alias.png)
### 二. Mysql/Mariadb 重设密码
#### 1. 操作步骤
```bash
/usr/local/opt/mariadb/bin/mysqld_safe --skip-grant-tables & # `mysqld_safe`需要全路径
mysql 
mysql > FLUSH PRIVILEGES;
mysql > SET PASSWORD FOR 'root'@'localhost' = PASSWORD('toor^dbpwd');
```
