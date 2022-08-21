# Ubuntu上使用virtualenv和virtualenvwrapper

`virtualenv`和`virtualenvwrapper`在Python虚拟环境中真是天作之合，我没有用过`pyenv`...

## 安装

如果你使用的是`python3`，那么你需要用`pip(python2)`来安装包

```bash
$ sudo pip3 install virtualenv virtualenvwrapper
```

## 配置

- 设置默认使用python版本

在`.profile`或者`.bashrc`文件中加入下行环境变量

```bash
$ echo 'export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3' >> .bashrc 
$ echo 'source /usr/local/bin/virtualenvwrapper.sh' >> .bashrc 
$ tail -2 .bashrc 
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
$ source .bashrc 
```

## 使用

- 创建一个新的virtualenv

```bash
$ mkvirtualenv as  # 因为已经指定了默认的Python版本，所以默认的是Python3
(as) ~$  # 创建成功之后会自动进入virtualenv中
```

- 退出virtualenv

在任意目录执行`deactivate`就可以退出

```bash
(as) ~$ deactivate 
~$ 
```

- 查看所有virtualenv

```bash
~$ workon 
as
```

- 在工作环境之间切换

```bash
~$ workon as
(as) ~$ 
```

- 删除一个virtualenv

```bash
(as) ~$ deactivate 
~$ rmvirtualenv as
Removing as...
~$ workon 
~$ 
```

## 参考文章

* [Virtual Environments](https://python-guide.readthedocs.io/en/latest/dev/virtualenvs/)
* [Virtualenv with Virtualenvwrapper on Ubuntu](http://roundhere.net/journal/virtualenv-ubuntu-12-10/)
