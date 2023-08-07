## 一.  容器网络问题
### 1. Docker  网络被默认'Host' 模式,  新启动容器无法暴露端口
> 解决办法 1:
* 容器启动时添加选项参数: '--network=host'
> 解决办法 2:
* 创建新的桥接网络: 
```bash
docker network create -d bridge new_bridge
```
* 容器启动指明容器网络类型:
```bash
docker run -d --network=new_bridge -p 8888:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v ./portainer/data:/data portainer/portainer-ce:latest
# 默认映射的端口如果和已有的端口冲突, 需要改用另一个或者用'-P' 选项
```

### 2. Docker 开启 cgroup  v2 特性
```bash
grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=1"
# 或 `GRUB_CMDLINE_LINUX`行`/etc/default/grub` 中添加`systemd.unified_cgroup_hierarchy=1`
grub2-mkconfig -o /boot/grub2/grub.cfg
# 报错可忽略
```
```json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "features": { "cgroupV2": true }
}
```