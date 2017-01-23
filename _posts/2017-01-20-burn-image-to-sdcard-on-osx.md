---
layout: post
title: MacOS下制作SDcard/USB系统启动器
description: 如何在MacOS下向SDcard/USB刻录系统
modified: 2017-01-20
tags: [工具]
---

很多人都知道如何在Windows系统上制作一个U盘启动器，因为有很多辅助工具。但如何在MacOS下，向SDcard/USB刻录系统，制作系统启动器？本文将以刻录Ubuntu系统，介绍两种方式：

#### 命令行(CLI)

+ ##### 步骤
  1. **下载Ubuntu系统**
  2. **转换iso文件为dmg文件**    
      *hdiutil convert -format UDRW -o /path/to/dmg /path/to/ubuntu.iso*    
      `-o 第一个参数为转换后的dmg文件路径(无需后缀)；第二个参数为下载的Ubuntu系统iso文件`
  3. **查看U盘标识**    
      *diskutil list*    
      注意挂载的U盘标识，如/dev/disk1
  4. **卸载U盘**    
      *diskutil unmountDisk /dev/disk1*
  5. **写入文件**    
      *sudo dd if=/path/to/ubuntu.dmg of=/dev/disk1 bs=1m*    
      `if的值为转换后的dmg文件全路径，of的值为需要写入的U盘标识，参考第3步的结果`
  6. **完成**    
      系统弹出提示无法读取U盘的信息框，点击“弹出”--制作成功

#### 工具(UI)

+ ##### 步骤
  1. **下载Ubuntu系统**
  2. **下载安装Etcher**
  3. **一键完成** ![Etcher-Intro](/assets/images/etcher.gif)

##### 资源
  + [Ubuntu下载][1]
  + [Etcher下载][2]

[1]: http://www.ubuntu.org.cn/download/desktop
[2]: https://etcher.io/
