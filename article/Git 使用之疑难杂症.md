## Git 使用之疑难杂症

### 一 .  Git 无法拉取项目
#### 1 .  报错关键字:  `key verification failed`
* 报错详细关键词句:  ` host key verification failed`  或  ` kex_exchange_identification `
解决步骤:
	参考链接: [生成新的 SSH 密钥并将其添加到 ssh-agent - GitHub 文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
```bash
# 清空 ~/.ssh/known_hosts 里 `github.com`开头的行
sed -i '/github.com/d' ~/.ssh/known_hosts
# 重新生成 ssh 密钥对
ssh-keygen -t ed25519 -C "your_email@example.com"
# 上面命令在交互提示时会要你输入密码, 可以直接回车留空, 但是后面步骤需要有调整配置
# 将 SSH 密钥添加到 ssh-agent
eval "$(ssh-agent -s)"
```
>  添加配置到 `~/.ssh/config`
```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  # IgnoreUnknown UseKeychain
  IdentityFile ~/.ssh/id_ed25519
```
> 如果你选择不向密钥添加密码，应该省略 `UseKeychain` 行,  加上 `IgnoreUnknown UseKeychain`
```bash
# 将 SSH 私钥添加到 ssh-agent 并将密码存储在密钥链中
/usr/bin/ssh-add -K ~/.ssh/id_ed25519
# ssh-add --apple-use-keychain ~/.ssh/id_ed25519
# 如果选择不向密钥添加密码，请运行命令，而不使用 `--apple-use-keychain` 选项。
```
> `ssh-add`  命令要使用 macOS 原生的, 非 brew 安装的, 否则会报错

> 将 SSH 公钥添加到 GitHub 上的帐户
```bash
pbcopy < ~/.ssh/id_ed25519.pub
# 公钥复制到剪贴板上
```
打开 [SSH and GPG keys (github.com)](https://github.com/settings/keys) , 点击 `New SSH key` ,  粘贴到输入框中,  注意后面的 *邮箱* 去掉

###  二.  Git 无法推送更新到项目
#### 1.  报错关键字: RPC  failed
* 报错详细关键词句: `error: RPC failed; HTTP 411 curl 22 The requested URL returned error: 411`
解决办法:
```bash
git config --global http.postBuffer 524288000
```

### 三.  终端(alacritty) 使用 mosh 报错: TERMINALS DATABASE IS INACCESSIBLE
 解决办法:
```bash
1. url -LO https://invisible-island.net/datafiles/current/terminfo.src.gz && gunzip terminfo.src.gz
2. # /usr/bin/tic -xe tmux-256color terminfo.src
3. /usr/bin/tic -xe alacritty-direct,tmux-256color terminfo.src
4. mv ~/.terminfo/74/tmux-256color ~/.terminfo/74/tmux-256color.bak
5. /usr/local/opt/ncurses/bin/infocmp -x tmux-256color > tmux-256color.src
6. sei '/pairs#0x10000/s/pairs#0x10000/pairs#0x32768/g' tmux-256color.src
7. tic -x tmux-256color.src
```