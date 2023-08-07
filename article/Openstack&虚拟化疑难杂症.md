### 1. IOMMU 特性检测告警
```bash
virt-host-validate #检测虚拟化特性是否开启
#  警告 (IOMMU appears to be disabled in kernel....
# 解决办法:
grubby --update-kernel=ALL --args=intel_iommu=on
vim /boot/efi/EFI/centos/grub.cfg
# 找到: linuxefi /vmlinuz-5.10.2-1.el7.elrepo.x86_64 root=/dev/mapper/centos00-root ro crashkernel=auto rd.lvm.lv=centos00/root rhgb quiet LANG=en_US.UTF-8
# 添加: intel_iommu=on
grub2-mkconfig -o /boot/grub2/grub.cfg
dracut --regenerate-all --force
reboot
```

### 2. Virsh list 执行报错
```bash
# 1. 报错内容: 连接到 '/var/run/libvirt/libvirt-sock' 失败: Connection refused
# nova_libvirt 容器启动失败或不健康状态
docker run -d quay.nju.edu.cn/openstack.kolla/nova-libvirt:master-rocky-9 nova_libvirt
```