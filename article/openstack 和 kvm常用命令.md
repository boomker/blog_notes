## 一. KVM 常用命令
### 1. 宿主机相关
####  a. 查看宿主机节点信息
```bash
virsh nodeinfo
```

### 2. 虚拟机相关
#### 1️⃣. 基础信息查询与虚拟机基础操作
#####  a️. 查看虚拟机列表
```bash
virsh list
```
##### b. 查看虚拟机基础信息
```bash
virsh dominfo instance-00000001
```
##### c. 虚拟机基础操作
```bash
# * 启动:
virsh start instance-00000006
# * 关机/销毁
virsh shutdown instance-00000006
virsh destroy instance-00000006
# * 删除虚拟机
virsh undefine instance-name  # 同时删除virsh列表里面的name与当前配置文件，删除的虚拟机必须是不活动的
virsh undefine --storage      # 目标文件，用逗号分开的目标或者源路径列表
virsh undefine --remove-all-storage # 删除虚拟机并删除所有磁盘文件
# * 克隆虚拟机
virt-clone -o instance-00000006  -n new_instance-00000007 -f alpine.iso
# * 挂起
virsh suspend instance-00000006
# * 恢复
virsh resume instance-00000006
# 配置自动启动
virsh autostart instance-00000006
# 导出虚拟机配置
virsh dumpxml instance-00000006 > /etc/libvirt/qemu/alpine.xml
# 修改虚拟机配置
virsh edit instance-00000006
# 创建虚拟机快照
virsh snapshot-create centos7
# 查看快照版本信息
virsh snapshot-current centos7
# 查看虚拟机快照信息
virsh snapshot-list centos7
# 恢复虚拟机快照到指定版本
virsh snapshot-revert centos7 snapshot-v1
# 删除快照
virsh snapshot-delete centos7 snapshot-v1
```
#### 2️⃣. 网络相关
##### a. 查看网络接口
```bash
virsh domiflist --domain instance-00000006
```
#### 3️⃣. CPU 相关
##### a. 查看 CPU 颗数
```bash
virsh vcpucount instance-00000006
```
#### 4️⃣. 存储相关
##### a. 查看磁盘列表
```bash
virsh domblklist instance-00000006
```
#### 5️⃣. IO 相关
##### a. 查看 io 线程信息
```bash
virsh iothreadinfo instance-00000006
```


