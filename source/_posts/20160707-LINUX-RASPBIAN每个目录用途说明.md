---
title: Linux/Raspbian每个目录用途说明
date: 2016-07-07 02:19:25
categories: Linux
tags: [linux, raspbian, 树莓派, 目录]
urlname: directory_introduction_in_linux_or_raspbian
---

## 文章更新

1. 20160707-初次成文

## 为什么会有这篇文章

因为在使用apt-get purge remove openvpn之后，发现系统里依然有很多openvpn名字的目录或者文件，比如像下面这样

``` bash
/usr/sbin/openvpn
/etc/default/openvpn
/etc/network/if-down.d/openvpn
/etc/network/if-up.d/openvpn
/etc/bash_completion.d/openvpn
/etc/init.d/openvpn
/run/openvpn
```

有强迫症+洁癖的我当然就很不爽了，挨个查看之后，就删掉了这些，但是对于一些目录的用户比较感兴趣，就找到了下面这篇文章，英文的，翻译了，留在这里，增加自己对LINUX系统的了解。<!-- more -->

## Linux和Windows的区别 

的显著区别之一就是其不同的目录结构，并不仅仅是格式上的不同，而是不同位置上保存的东西区别很大。

在Windows中，典型的路径可能是这样的 `D:\Folder\subfolder\file.txt`，而在Linux中，路径则是这样的 `/Folder/subfolder/file.txt`。

斜线倾斜的方向不同，并且，在Linux中，也没有C盘D盘的概念，Linux系统启动之后，`根分区` 就"挂载"在了在了 `/` 的位置，并且所有的文件、文件夹、设备以及不同的硬盘光驱之类的，也都挂载在了 `/`。

虽然可能在下面这个例子中并不明显，但是Linux系统对文件或者文路径的名称中的大小写字符是敏感的。

比如 `/Folder/subfolder/file.txt` 与 `/folder/subfolder/file.txt`并不是同一个文件。

## Linux系统目录说明

Unix中和Linux的目录结构是一个统一的目录结构，所有的目录和文件最终都统一到"/"根文件系统下。文件系统是无论是不是挂载过来的，最终都分层排列到以"/"为起始的文件系统之下。
Linux目录结构遵循"文件系统层次结构（Filesystem Hierarchy Structure，FHS)"，这标准是由“自由标准组织（Free Standards Group）”进行维护的，然而大多数LINUX发行版都有意或者无意的与这一规范背离。

"/" 根路径

这是Linux系统的“根”目录，也是所有目录结构的最底层。在UNIX以及和它兼容的系统中，"/"是一个单独的目录。

/boot

这个目录下包含系统启动文件（boot loader），例如Grub，Lilo或者Kernel，以及initrd，system.map等配置文件。

> Initrd ramdisk或者""initrd""是指一个临时文件系统，它在启动阶段被Linux内核调用。initrd主要用于当“根”文件系统被挂载之前，进行准备工作。

/sys

这个目录下包含内核、固件以及系统相关文件。

/sbin

包含系统操作和运作所必需的二进制文件以及管理工具，主要就是可执行文件。类似WINDOWS下的EXE文件。

/bin

包含单用户模式下的二进制文件以及工具程序，比如cat，ls，cp这些命令。

/lib

包含/sbin和/bin目录下二进制文件运行所需要的库文件。

/dev

内含必需的系统文件和驱动器。

/etc

内含系统配置文件，其下的目录，比如 /etc/hosts, /etc/resolv.conf, nsswitch.conf, 以及系统缺省设置，网络配置文件等等。以及一些系统和应用程序的配置文件。

/home

每一个用户的在这个目录下，都会单独有一个以其用户名命令的目录，在这里保存着用户的个人设置文件，尤其是以 profile结尾的文件。但是也有例外，root用户的数据就不在这个目录中，而是单独在根路径下，保存在单独的/root文件夹下。

/media

一个给所有可移动设备比如光驱、USB外接盘、软盘提供的常规挂载点。

/mnt

临时文件系统挂载点。比如，你并不想长期挂载某个驱动器，而是只是临时挂载一会U盘烤个MP3之类的，那么应该挂载在这个位置下。

/opt

在Linux系统中，这个目录用到的并不多，opt是 可选系统程序包（Optional Software Packages）的简称。这个目录在UNIX系统，如Sun Solaris用途要广泛的多。

/usr

用户数据目录，包含了属于用户的实用程序和应用程序。这里有很多重要的，但并非关键的文件系统挂载这个路径下面。在这里，你会重新找到一个 bin、sbin 和 lib目录，其中包含非关键用户和系统二进制文件以及相关的库和共享目录，以及一些库文件。

/usr/sbin

包含系统中非必备和并不是特别重要的系统二进制文件以及网络应用工具。

/usr/bin

包含用户的非必备和并不是特别重要的二进制文件。

/usr/lib

保存着/usr/sbin以及/usr/bin中二进制文件所需要的库文件。

/usr/share

"平台无关"的共享数据目录。

/usr/local

是/usr下的二级目录，这里主要保存着包含系统二进制文件以及运行库在内的本地系统数据。

/var

这个路径下通常保存着包括系统日志、打印机后台文件（spool files）、定时任务（crontab）、邮件、运行进程、进程锁文件等。这个目录尤其需要注意进行日常的检查和维护，因为这个目录下文件的大小可能会增长很快，以致于很快占满硬盘，然后导致系统便会出现各种奇奇怪怪的问题。

/tmp

顾名思义，这是一个临时文件夹，专门用来保存临时文件，每次系统重启之后，这个目录下的"临时"文件便会被清空。同样，/var/tmp 也同样保存着临时文件。两者唯一的不同是，后者 /var/tmp目录保存的文件会受到系统保护，系统重启之后这个目录下的文件也不会被清空。

/proc 

这个目录是驻留在系统内存中的虚拟（psuedo，伪）文件系统，其中保存的都是文本格式的系统内核和进程信息。

## LINUX系统目录结构图

![Linux Directory Structure in Visual View](ldr.png)

需要注意的是，不同LINUX发行版本的目录结构会有一些差异，这对LINUX新手来说比较纠结，但是大体上，所以LINUX的不同发行版本，都符合上面这幅图片中的路径结构。

## 参考文章

1.[Linux Directory Structure Overview](http://www.debianadmin.com/linux-directory-structure-overview.html)


