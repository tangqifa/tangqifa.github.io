---
layout: post
title: "完全重新安装Yosemite正式版"
date: 2014-10-08 13:41:54 +0800
comments: true
categories: os 
---
之前由于自己的mac用过一年多了，垃圾文件比较多，各种软件以及环境参数的配置也很乱，趁Yosemite正式版刚好发布了，就想完全重新安装，现把安装过程和所遇到的问题记录如下。

在完全重新安装之前，我是用AppStore升级安装过一次，不过升级之后，几个常用的软件都不能用了，经池建强老师的Yosemite上篇文章的指点，这些软件都能用了，但是还是有一些配置环境参数问题，遂决定完全重装一次。

经网上搜一大堆教程，想要完全重装，必须开机同时按住`option`或者`command + R`然后把硬盘东西全部抹去，这样一来，问题来了，系统没了，完全启动不了，万幸的是我之前已经硬盘资料备份了，但是没有做系统备份，现在也只有通过网络恢复了，但是等待数小时的结果是，网络异常，在线更新恢复失败。故，只要拿出杀手锏，U盘安装。

##安装前准备

- 16G U盘一个
- Windows电脑一台（如果你还有另一台mac电脑，就不需要下面的镜像烧制软件了，mac自带就有，也方便得多）
- Yosemite系统镜像一份 [Yosemite 14A389 正式版](http://pan.baidu.com/s/1mgqSdfy)
- 镜像烧制软件 [TransMac](http://www.acutesystems.com/scrtm.htm)付费软件，试用期15天，完全够用
<!-- more -->
##安装详细过程

- 以管理员身份打开TransMac，如下图
![TransMac截图](http://kejiwen.com/wp-content/uploads/2014/10/TransMac.jpg)
- 格式化U盘为Mac格式，如图第一个红框 Format Disk for Mac
- 然后Restore with Disk Image，选择下载好的Yosemite镜像
- 大概等5分钟左右，就完成写入，U盘系统制作完毕
- 把U盘插上要安装的Mac上，开机按住 `option`选择U盘即可开始安装了。

## Android开发环境配置

- IDE下载 [Android studio](https://dl.google.com/android/studio/install/0.8.6/android-studio-bundle-135.1339820-mac.dmg) 或则[ADT](https://dl.google.com/android/adt/adt-bundle-mac-x86_64-20140702.zip)
- JDK1.6 [苹果官方提供安装包](http://support.apple.com/kb/dl1572)
- 安装好jdk1.6后大多数软件10.9.5下不能正常工作的软件基本都能正常运行了

## 终端配置
- iTerm [下载](http://iterm2.com/)
- 编辑`/etc/bashrc`文件，将`PS1的值`改为`\W\$`主要是解决名称过长问题

## 其他软件
- 如下图，大家按需下载，反正我基本上就这些
![TransMac截图](http://kejiwen.com/wp-content/uploads/2014/10/soft-set.png)

以上，基本就是我全新安装Yosemite的全过程。
