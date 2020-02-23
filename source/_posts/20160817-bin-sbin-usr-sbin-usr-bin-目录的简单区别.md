---
title: /bin /sbin /usr/sbin /usr/bin 目录的简单区别
date: 2016-08-17 02:27:17
categories: Linux
tags: [linux, 目录]
urlname: the_difference_between_bin_sbin_and_those_under_usr_folder_in_linx
---

## 文章更新

1. 20160817-初次成文

## 为什么会有这篇文章

因为一直不是很明白这几个目录有什么区别，尤其是bin和sbin的区别，因此转载了这篇文章，方便自己记忆。<!-- more -->

这些目录都是存放命令的，首先区别下`/sbin`和`/bin`：

从命令功能来看，`/sbin` 下的命令属于基本的系统命令，如shutdown，reboot，用于启动系统，修复系统，`/bin` 下存放一些普通的基本命令，如`ls`, `chmod` 等，这些命令在Linux系统里的配置文件脚本里经常用到。

从用户权限的角度看，`/sbin` 目录下的命令通常只有管理员才可以运行，`/bin` 下的命令管理员和一般的用户都可以使用。

从可运行时间角度看，`/sbin`, `/bin` 能够在挂载其他文件系统前就可以使用。

而`/usr/bin`, `/usr/sbin` 与 `/sbin` `/bin`目录的区别在于：

`/bin`, `/sbin` 目录是在系统启动后挂载到根文件系统中的，所以`/sbin`, `/bin` 目录必须和根文件系统在同一分区；

`/usr/bin`, `usr/sbin` 可以和根文件系统不在一个分区。

`/usr/sbin` 存放的一些非必须的系统命令；`/usr/bin` 存放一些用户命令，如led(控制LED灯的)。

转下一位网友的解读，个人认为诠释得很到位：

`/bin` 是系统的一些指令。bin为binary的简写主要放置一些系统的必备执行档例如:cat、cp、chmod df、dmesg、gzip、kill、ls、mkdir、more、mount、rm、su、tar等。

`/sbin` 一般是指超级用户指令。主要放置一些系统管理的必备程式例如:cfdisk、dhcpcd、dump、e2fsck、fdisk、halt、ifconfig、ifup、 ifdown、init、insmod、lilo、lsmod、mke2fs、modprobe、quotacheck、reboot、rmmod、 runlevel、shutdown等。

`/usr/bin` 是你在后期安装的一些软件的运行脚本。主要放置一些应用软体工具的必备执行档例如c++、g++、gcc、chdrv、diff、dig、du、eject、elm、free、gnome*、 gzip、htpasswd、kfm、ktop、last、less、locale、m4、make、man、mcopy、ncftp、 newaliases、nslookup passwd、quota、smb*、wget等。

`/usr/sbin` 放置一些用户安装的系统管理的必备程式例如:dhcpd、httpd、imap、in.*d、inetd、lpd、named、netconfig、nmbd、samba、sendmail、squid、swap、tcpd、tcpdump等。

如果新装的系统，运行一些很正常的诸如：shutdown，fdisk的命令时，悍然提示：`bash:command not found`。那么首先就要考虑root 的`$PATH`里是否已经包含了这些环境变量。可以查看PATH，如果是：`PATH=$PATH:$HOME/bin` 则需要添加成如下：

``` bash
PATH=$PATH:$HOME/bin:/sbin:/usr/bin:/usr/sbin
```

## 参考文章

1. [/bin,/sbin,/usr/sbin,/usr/bin 目录之简单区别](http://blog.csdn.net/kkdelta/article/details/7708250)

