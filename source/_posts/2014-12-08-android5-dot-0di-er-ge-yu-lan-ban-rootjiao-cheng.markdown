---
layout: post
title: "Android5.0第二个预览版Root教程"
date: 2014-11-13 13:43:55 +0800
comments: true
categories: android 
---
## 1.刷官方ROM
 - 终端下运行命令：`adb reboot-bootloader`
 - 解压官方最新下载包，运行脚本：`./flash-all.sh`即可一键刷机
 
## 2.刷第三方Recovery
 终端运行命令：`fastboot flash recovery recovery-clockwork-6.0.4.5-hammerhead.img`为第四步做准备
 
## 3.刷第三方内核
终端运行命令：`fastboot flash boot boot.img`，由于SElinux安全的限制，目前只能通过刷修改过的内核，实现root
## 4.Recovery Mode下刷入SuperSU.zip
终端运行命令：`adb reboot recovery`

以上所要用到的全部软件下载地址：[百度网盘](http://pan.baidu.com/s/1vqkCu)
