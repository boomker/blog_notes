# Ubuntu安装配置SSH服务

每次安装好Ubuntu之后就要配置SSH服务，每次配置SSH服务都还记不住命令，无奈只有写篇文章方便以后查看。

Ubuntu安装好后默认是没有安装SSH服务的，需要我么手动安装配置SSH。

安装SSH服务端

```bash
ansheng@ansheng-me:~$ sudo apt-get install openssh-server
```

安装SSH客户端

貌似默认自带的就有客户端，如果没有就安装

```bash
ansheng@ansheng-me:~$ sudo apt-get install openssh-client
```

启动SSH服务

```bash
ansheng@ansheng-me:~$ sudo systemctl start ssh.service
```

检查启动状态

```bash
ansheng@ansheng-me:~$ sudo netstat -tlnp | grep "22"
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      3735/sshd       
tcp6       0      0 :::22                   :::*                    LISTEN      3735/sshd 
```

设置开机启动

```bash
ansheng@ansheng-me:~$ sudo systemctl enable ssh.service
Synchronizing state of ssh.service with SysV init with /lib/systemd/systemd-sysv-install...
Executing /lib/systemd/systemd-sysv-install enable ssh
```

连接测试

```bash
ansheng@ansheng-me:~$ ssh 127.0.0.1
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:8KZ4QUV0YNp2c487rOalUMuVD44aHDnTHwCwQ6H6HCE.
Are you sure you want to continue connecting (yes/no)? 
```

出现这个提示就完成了