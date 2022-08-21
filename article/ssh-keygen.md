# ssh-keygen批量分发

## 环境介绍

|IP|主机名|说明|
|:--|:--|:--|
|172.16.10.10|ansheng|分发服务器|
|172.16.10.100|zabbix-node01|node1|
|172.16.10.101|zabbix-node02|node2|

系统环境：

```bash
[root@ansheng ~]# cat /etc/redhat-release 
CentOS release 6.7 (Final)
[root@ansheng ~]# uname -a
Linux ansheng 2.6.32-573.22.1.el6.x86_64 #1 SMP Wed Mar 23 03:35:39 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
```

## 分发服务器生成Key

非交互式生成

```bash
[root@ansheng ~]# ssh-keygen -t rsa -f ~/.ssh/id_rsa -P ''
Generating public/private rsa key pair.
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
1b:81:ad:d8:95:41:02:70:a0:b2:6f:aa:2c:17:4e:44 root@ansheng
The key's randomart image is:
+--[ RSA 2048]----+
|  ooo...o        |
| .E.   + o       |
|o.    . =        |
|...  o o .       |
|..  . o S        |
| .o      o       |
| oo.    .        |
|ooo              |
|=o               |
+-----------------+
```

## 把公钥发给客户端

### 发送第一个节点

```bash
[root@ansheng ~]# ssh-copy-id -i ~/.ssh/id_rsa.pub root@172.16.10.100
The authenticity of host '172.16.10.100 (172.16.10.100)' can't be established.
RSA key fingerprint is 55:f1:c0:57:1e:a2:96:1b:bb:0d:85:03:d0:e7:56:42.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.10.100' (RSA) to the list of known hosts.
Address 172.16.10.100 maps to bogon, but this does not map back to the address - POSSIBLE BREAK-IN ATTEMPT!
root@172.16.10.100's password: 
Now try logging into the machine, with "ssh 'root@172.16.10.100'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.
```

### 发送第二个节点

```bash
[root@ansheng ~]# ssh-copy-id -i ~/.ssh/id_rsa.pub root@172.16.10.101
The authenticity of host '172.16.10.101 (172.16.10.101)' can't be established.
RSA key fingerprint is 55:f1:c0:57:1e:a2:96:1b:bb:0d:85:03:d0:e7:56:42.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.10.101' (RSA) to the list of known hosts.
Address 172.16.10.101 maps to bogon, but this does not map back to the address - POSSIBLE BREAK-IN ATTEMPT!
root@172.16.10.101's password: 
Now try logging into the machine, with "ssh 'root@172.16.10.101'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.
```

如果ssh服务器端口非22可以使用以下方式发送：

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub "-p 235  root@172.16.10.101"
```

## 登陆客户端执行命令

```bash
[root@ansheng ~]# ssh root@172.16.10.100 /sbin/ifconfig eth0
Address 172.16.10.100 maps to bogon, but this does not map back to the address - POSSIBLE BREAK-IN ATTEMPT!
eth0      Link encap:Ethernet  HWaddr 00:0C:29:3D:95:D8  
          inet addr:172.16.10.100  Bcast:172.16.10.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe3d:95d8/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:12489 errors:0 dropped:0 overruns:0 frame:0
          TX packets:4936 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:16654811 (15.8 MiB)  TX bytes:339180 (331.2 KiB)
```

如果端口非22可以使用一下指令：

```bash
ssh -p235 root@172.16.10.100 /sbin/ifconfig eth0
```

## 遇到的问题

在登陆客户端或执行指令的时候遇到以下哎问题：

```bash
[root@ansheng ~]# ssh root@172.16.10.101
Address 172.16.10.101 maps to bogon, but this does not map back to the address - POSSIBLE BREAK-IN ATTEMPT!
Last login: Tue Apr 26 08:46:55 2016 from 172.16.10.1
[root@zabbix-node02 ~]# 
```

解决方法：

修改node2节点上面的sshd配置文件，

```bash
[root@zabbix-node02 ~]# sed -i 's#GSSAPIAuthentication yes#GSSAPIAuthentication no#g' /etc/ssh/sshd_config 
[root@zabbix-node02 ~]# /etc/init.d/sshd restart
Stopping sshd:                                             [  OK  ]
Starting sshd:                                             [  OK  ]
```

登陆测试：

```bash
[root@ansheng ~]# ssh root@172.16.10.101
Last login: Tue Apr 26 08:46:57 2016 from 172.16.10.10
```

这次就没有提示了。