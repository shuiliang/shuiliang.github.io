---
layout: post
title: ' 同一台Mac上配置多个 SSH-Key'
subtitle: '配置多个SSH-Key'
date: 2018-08-21-同一台Mac上配置多个SSH-Key
author: 黄水亮
cover: 'https://cdna.artstation.com/p/assets/images/images/008/107/456/large/lee-souder-rg-001.jpg?1510539788'
tags: Git  SSH-Key
---

Secure Shell（安全外壳协议，简称SSH）是一种加密的网络传输协议，可在不安全的网络中为网络服务提供安全的传输环境；SSH-Key 可以方便的登录到 SSH 服务器，而无需输入密码。由于无需发送密码到网络中,，被认为是更加安全的方式。由于我在公司使用的是Git进行代码管理，而在公司电脑上有时需要访问 [Github](https://github.com/shuiliang)和 [Gitee](https://gitee.com/shuiliang) 。所以，在同一台机器上配置两个 SSH-Key 很有必要。




## 1.生成 SSH-Key

命令行输入 `shuiMac:~ huangshuiliang$ ssh-keygen -t rsa -C "your@email.com" -f ~/.ssh/id_rsa` 其中，-f 参数表示生成 SSH-Key，如果不设置，代表默认路径为配置多个 SSH-Key 必须设置路径，否则会相互覆盖。

```swift
//key-1
ssh-keygen -t rsa -C "git@github.com" -f ~/.ssh/id_rsa

//key-2
ssh-keygen -t rsa -C "git@gitee.com" -f ~/.ssh/id_rsa_new

```

- key-1事例:

```swift
shuiMac:~ huangshuiliang$ ssh-keygen -t rsa -C "git@github.com" -f ~/.ssh/id_rsa
Generating public/private rsa key pair.

/Users/huangshuiliang/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/huangshuiliang/.ssh/id_rsa.
Your public key has been saved in /Users/huangshuiliang/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:gOq1Lsa6RcINAaC8kGt0E6pzuU8gLysPpAVsRpxKMug git@github.com
The key's randomart image is:
+---[RSA 2048]----+
|Bo..             |
|O=o ..           |
|XX o. .          |
|OE=o.  .         |
|*=B..   S        |
|+X + .           |
|+.= o            |
|.=++             |
|==..o            |
+----[SHA256]-----+

```

## 2.Git服务器设置 SSH-Key

#### 1.命令行复制生成的 SSH-Key

```swift
//key-1
shuiMac:~ huangshuiliang$ pbcopy < ~/.ssh/id_rsa.pub

//key-2
shuiMac:~ huangshuiliang$ pbcopy < ~/.ssh/id_rsa_new

```
- key-1事例:

```swift
shuiMac:~ huangshuiliang$ pbcopy < ~/.ssh/id_rsa.pub
```

#### 2.登录 Git 服务器，配置 SSH-Key

以Github为例,点击右上角Settings，进入到Personal settings ->SSH and GPG keys -> New SSH Key, 粘贴SSH-Key即可。

![](/assets/img/details/2018-08-21/1.png)![](/assets/img/details/2018-08-21/2.png)


## 3.设置 SSH-Key 代理

```swift
//key-1
ssh-add ~/.ssh/id_rsa

//key-2
ssh-add ~/.ssh/id_rsa_new

```

## 4.增加/修改 .ssh/config 配置文件

在 ~/.ssh 目录下添加 config 配置文件用于区分多个 SSH-Key

![](/assets/img/details/2018-08-21/3.png)![](/assets/img/details/2018-08-21/4.png)

```swift
# 添加config配置文件
# vi ~/.ssh/config

# 文件内容如下：
# github服务器
Host github.com
HostName github.com
User test1
IdentityFile ~/.ssh/id_rsa

# gitee服务器
Host gitee.com
HostName gitee.com
User teste
IdentityFile ~/.ssh/id_rsa_new

# 配置文件参数
# Host : Host可以看作是一个你要识别的模式，对识别的模式，进行配置对应的的主机名和ssh文件
# HostName : 要登录主机的主机名
# User : 登录名
# IdentityFile : 指明上面User对应的identityFile路径
```

## 5.验证结果

```swift
//key-1
ssh -T git@github.com

//key-2
ssh -T git@gitee.com

```
要是输出提示`Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.`说明 SSH-Key 配置成功！

- key-1事例:

```swift
shuiMac:.ssh huangshuiliang$ ssh -T git@github.com
Hi shuiliang! You've successfully authenticated, but GitHub does not provide shell access.
```

