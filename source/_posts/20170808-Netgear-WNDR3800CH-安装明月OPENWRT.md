---
title: Netgear WNDR3800CH 安装明月OPENWRT
categories: 折腾
tags: [明月永在, openwrt]
date: 2017-08-09 02:58:17
urlname: install_mingyue_openwrt_fireware_on_netgear_wndr3800ch
---

## 文章更新
1. 20170808-初次成文

## 为什么会有这篇文章
最近的Shadowsocks不稳定了，而且闹得网络也不稳定了，路由器是NETGEAR的WNDR3800CH型号，之前本来买的时候就是带的明月永在的OPENWRT固件，但是后来为了稳定，就刷回了WNDR的原厂固件，现在又想刷回明月固件，破费了一番周折。

## 安装前的准备工作
首先需要明确一点的就是，如果WNDR3800的原厂固件，版本高于1.0.0.16，就必须刷回这个版本的。以我为例，当初升到了最新的固件1.0.0.48，直接上传明月的`openwrt-15.05.1-ar71xx-generic-wndr3800ch-squashfs-factory.img`不成功，去OPENWRT的官方论坛上，破费了一点时间，才发现了这个问题。

## 原厂固件降级
这个没啥好说的，找到1.0.0.16的固件文件，然后进入到路由器界面（默认地址是`192.168.0.1`），依次进入 `ADVANCED`-`Administration`-`Firmware Update`，上传你的文件，会弹出一个提示，告诉你上传的固件版本要低于当前的版本，不要理解，直接确定即可，等10分钟左右，就可以了。

需要注意的是，固件刷完之后，路由的原来配置不变，账号密码依然是之前的。

## 上传明月OPENWRT固件
上传我上面提到的那个明月编译的固件，这个时候如果还提示上传失败（Upload fail），则需要擦除路由器的全部设置，使其恢复出厂设置，要做到这个可以有两种方法，一种是暴力点的，找个大头针去戳路由器背后的RESET按钮，超过30秒。第二种温柔一些，继续在路由器界面里，找到ADMINISTRATION BACKUP SETTING里面，最后一下，RESTORE FACTORY SETTING，等上1分钟即可。

差点忘记了，恢复出厂设置后，路由器的IP地址，变为了192.168.1.1（我之前设置成了192.168.0.1）

然后，继续上传明月固件。 

## 如果刷明月失败，可以考虑LEDE

LEDE是一个在OPENWRT基础上开发出来的全新固件，其实和OPENWRT对应的观察不多，也有类似的文件，所以我直接从LEDE的网站下载了，[地址](https://downloads.lede-project.org/releases/17.01.2/targets/ar71xx/generic/)。

## 参考文章

1. [每天一个linux命令（35）：ln 命令](http://www.cnblogs.com/peida/archive/2012/12/11/2812294.html)