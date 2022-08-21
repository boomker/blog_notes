# CentOS升级Python到最新版本

查看自带Python版本

```bash
[root@Ansheng ~]# python -V
Python 2.6.6
```

下载Pyhon，选择下载`Gzipped source tar ball`

网址https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tgz

```bash
[root@Ansheng ~]# wget https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tgz
--2015-12-02 11:22:22-- https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tgz
Resolving www.python.org... 103.245.222.223
Connecting to www.python.org|103.245.222.223|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 16768806 (16M) [application/octet-stream]
Saving to: “Python-2.7.10.tgz”
100%[=================================================================================================================================&gt;] 16,768,806 1.29M/s in 12s
2015-12-02 11:22:34 (1.33 MB/s) - “Python-2.7.10.tgz” saved [16768806/16768806]
```

解压安装，命令如下

```bash
[root@Ansheng ~]# ll
total 16420
-rw-------. 1 root root 1417 Dec 2 10:19 anaconda-ks.cfg
-rw-r--r--. 1 root root 26654 Dec 2 10:19 install.log
-rw-r--r--. 1 root root 6797 Dec 2 10:18 install.log.syslog
-rw-r--r-- 1 root root 16768806 May 24 2015 Python-2.7.10.tgz
[root@Ansheng ~]# tar xf Python-2.7.10.tgz
[root@Ansheng ~]# cd Python-2.7.10
[root@Ansheng Python-2.7.10]# ./configure --prefix=/usr/local/python2.7
[root@Ansheng Python-2.7.10]# make
[root@Ansheng Python-2.7.10]# make install
#如编译失败请检查是否已安装开发工具包，如果没有安装请使用以下命令进行安装
[root@Ansheng ~]# yum -y groupinstall "debugging Tools"
```
创建链接来使系统默认python变为python2.7
```bash
[root@Ansheng Python-2.7.10]# ln -fs /usr/local/python2.7/bin/python2.7 /usr/bin/python
```
查看Python版本
```bash
[root@Ansheng Python-2.7.10]# python -V
Python 2.7.10
```
修改yum配置（否则yum无法正常运行）
```bash
[root@Ansheng Python-2.7.10]# vim /usr/bin/yum
#!/usr/bin/python2.6
```

将第一行的`#!/usr/bin/python`修改为系统原有的python版本地址`#!/usr/bin/python2.6`