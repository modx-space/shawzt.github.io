---
layout: post
title:  为多个Github账号配置SSH keys
description: SSH keys with multiple Github accounts
modified: 2020-01-04
tags: tool tech
---

### 概要
最近遇到一个需要在同一台Mac上配置多个SSH Key以访问Github上多个不同账号的问题，场景如下：
1. 公司项目托管在Github（https://github.com/）上，需要使用公司的邮箱注册账号并使用
2. 个人项目也用Github（https://github.com/）管理，使用个人邮箱注册的账号访问

这样会遇到什么问题呢？   
在未通过下述步骤配置时，当你在不同的项目间切换并尝试去更新代码时，将会遇到认证失败的提示。

### 方案
#### 创建SSH Keys
1. 请参照[Github SSH Key帮助文档-1][1]来生成新SSH密钥
2. 密钥文件的默认名是`id_rsa`，为更好的管理密钥，请分别命名如下：
  + 将公司账号（邮箱）对应生成的密钥文件命名为`id_rsa_work`
  + 将个人账号（邮箱）对应生成的密钥文件命名为`id_rsa_personal`

#### 将SSH Keys添加到ssh-agent
1. 请参照[Github SSH Key帮助文档-2][2]将上述创建的SSH密钥添加到ssh-agent
  + 启动ssh-agent：`eval "$(ssh-agent -s)"`
  + 添加公司账号对应的密钥：`ssh-add ~/.ssh/id_rsa_work`
  + 添加个人账号对应的密钥：`ssh-add ~/.ssh/id_rsa_personal`

#### 将SSH Keys添加到Github账号
1. 请参照[Github SSH Key帮助文档-3][3]将上述创建的SSH密钥添加到Github账号
  + 复制公司账号对应的密钥：`pbcopy < ~/.ssh/id_rsa_work.pub`
  + 复制个人账号对应的密钥：`pbcopy < ~/.ssh/id_rsa_personal.pub`

#### 创建SSH配置文件
现在我们在`~/.ssh`目录下创建一个配置文件来告诉git该使用哪个SSH密钥来访问指定的资源。
+ 检查或新建`~/.ssh/config`文件
```
// 新建
touch ~/.ssh/config
```
+ 配置信息
  - 任何使用`me.github.com`的git url将使用`id_rsa_personal`密钥
```
Host me.github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_personal
```
  - 任何使用`work.github.com`的git url将使用`id_rsa_work`密钥
```
Host work.github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_work
```

#### 克隆项目
1. Github个人项目的原生ssh clone url是：`git@github.com:shawzt/leetcode-java.git`
2. 根据上述配置，请使用`me.github.com`替换`github.com`
3. 命令：`git clone git@me.github.com:shawzt/leetcode-java.git`
4. 同此，对于公司项目，请使用`work.github.com`替换`github.com`

---

[1]: https://help.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key
[2]: https://help.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent
[3]: https://help.github.com/cn/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account
