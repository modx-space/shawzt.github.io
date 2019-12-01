---
layout: post
title:  配置自定义域名
description: Enable Custome Subdomain
modified: 2019-12-01
tags: tech personal
---

### 概要
最近刚从服务商[GoDaddy][1]那购买了打算已久的域名，为了不让这宝贵的资源闲置，也让自己这常年不更新的博客显得‘高端’些，所以就把域名的首次应用配置到基于[Jekyll][2] + [Github Pages][3]搭建起来的个人博客上。

以下是具体的操作步骤：
(这里略过域名购买以及博客搭建)

### 域名DNS配置
1. 登录GoDaddy，进入我的域名管理：![domain](/assets/images/domain.png)
2. 在需要设置的域名下，点击’管理DNS‘
3. 添加CNAME记录：![domain](/assets/images/dns-subdomain.png)
  + 类型选择：`CNAME`
  + 主机：就是subdomain，如`www`，`blog`等
  + 指向：就是默认的`<user>.github.io`

### 博客配置
+ __添加CNAME__   
  1. 在博客repo的根目录下，新建文件`CNAME`（没有任何后缀名）
  2. 添加CNAME记录，我的配置就是：`blog.modx.space`
  3. 提交并推送改动

+ __Github Pages__   
  1. 登录github，并进入博客repo的设置
  2. 在上述CNAME改动提交并推送后，就可以在*Github Pages*部分看到自定义的域名已配置成功
  3. 为博客开启*HTTPS*
  ![domain](/assets/images/github-pages.png)

### 验证
1. 运行`dig`命令：  
  + ![domain](/assets/images/dig-github-pages.png)
  + ![domain](/assets/images/dig-subdomain.png)

> 参考: [关于DNS][4]
---

[1]: https://godaddy.com/
[2]: http://jekyllrb.com/
[3]: https://pages.github.com/
[4]: /posts/what-is-dns
