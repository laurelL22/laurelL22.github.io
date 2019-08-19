---
title: 配置多个SSH-Key
date: 2019-08-14 15:39:39
tags:
---

### 起源

个人的github和公司的gitlab需要配置不同的SSH-Key.这两者的邮箱如果不同，就会造成：生成第二个git的key的时候会覆盖第一个的key，导致必然有一个无法使用。

### 解决

- 为**github**生成ssh key
    > ssh-keygen -t rsa -C "mail@example.com" -f ~/.ssh/github_rsa

- 为**gitlab**生成ssh key
    >ssh-keygen -t rsa -C "mail@example.com" -f ~/.ssh/gitlab_rsa

- 前往gitlab站添加SSH公钥(gitlab_rsa); 前往github站添加SSH公钥(github_rsa)

- 添加配置文件
    > vim ~/.ssh/config

- 配置文件内容
```
# github.com
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_rsa

# gitlab.company.com
Host gitlab.company.com
HostName gitlab.company.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitlab_rsa
```

- 测试：
    > github: ssh -T git@github.com
    > github: ssh -T git@gitlab.company.com

### 其他
- 添加私钥：
    > ssh-add ~/.ssh/github_rsa
    > ssh-add ~/.ssh/gitlab_rsa
- 确私钥列表
    > ssh-add -l