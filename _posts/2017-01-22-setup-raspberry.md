---
layout: post
title: 激活你的树莓派(Raspberry Pi)
description: 树莓派2代的系统安装和运行
modified: 2017-01-22
tags: [树莓派]
---

树莓派(Raspberry Pi)是一款基于ARM的，仅有一张信用卡大小的电脑主板，以microSD卡做为启动、储存磁盘，且内置很多接口，如音/视频、USB、LAN等，见下图 _[图片来自网络]_。它由英国树莓派基金会所开发推广，目的是以低价硬件及自由软件刺激学校的基本计算机科学教育，更多请参考[Wiki][1]。![Image of Raspberry Pi](/assets/images/raspberry_pi.png)

对硬件及计算机的爱好者而言，Raspberry Pi是一把万能的“瑞士军刀”，他们可以借助Raspberry Pi做很多有趣的扩展。作为一名“攻城狮”，终究还是没忍住，入手了一枚Raspberry Pi 2且配件齐备，无图无真相:![Image of My Raspberry Pi 2](/assets/images/my_raspberry_pi_2.png)

本文就与大家分享一下：**如何激活你的Raspberry Pi** _(以Raspberry Pi 2及Raspbian OS为例)_

#### 材料清单
1. Raspberry Pi 2及其电源
2. microSD及读卡器
3. 网线
4. 一台能访问Internet计算机

#### 技能清单
+ 计算机基础知识
+ Linux基本命令

#### 安装系统
1. 下载官方的Raspbian系统，更多支持的系统可参考[官方说明][3]
2. 将系统刻录进SDcard，具体步骤可参考[如何在MacOS下向SDcard/USB刻录系统][4]

#### 启动Raspberry Pi
1. 将写入系统后的microSD插入Raspberry Pi的SD卡槽中
2. 用网线连接Raspberry Pi和路由器
3. 接通电源

#### 登录Raspberry Pi
1. 进路由器管理页面，查看Raspberry Pi的IP
2. 在Mac上使用SSH登陆Raspberry Pi   
  **$ssh pi@xxx.xxx.xxx.xxx**    
_默认用户名:pi;密码:raspberry_

![Image of SSH To Raspberry Pi 2](/assets/images/ssh_raspberry_pi.png)

##### 资源
  + [Raspbian系统][2]

[1]: https://zh.wikipedia.org/wiki/%E6%A0%91%E8%8E%93%E6%B4%BE
[2]: https://www.raspberrypi.org/downloads/raspbian/
[3]: https://www.raspberrypi.org/downloads/
[4]: /posts/burn-image-to-sdcard-on-osx
