---
title: 关于cron和crontab的那些事
date: 2016-07-25 16:03:57
categories: Linux
tags: [linux, cron, crontab, rapsberry]
urlname: what_you_need_to_know_about_cron_and_crontab
---

## 文章更新

1. 20160725-初次成文

## 为什么会有这篇文章

因为目前用的raspbian比较多，其次多的就是centos了，都需要涉及到自动任务的环节，但是有一些自己认识上模糊的地方，还需要好好的系统明确一下。所以才有了这个帖子。<!-- more -->

## 什么是cron和crontab

不说废话了，大家可以通过 man cron 和 man crontab 来查看系统文档，但是简单的说，可以这样理解，cron是系统执行循环任务的任务名称，crontab是具体的文件。

但是可以执行的，在树莓派中有3个地方，分别是 `/etc/cron.d` 目录、 `/etc/crontab` 文件以及 `/var/spool/cron/crontabs` 目录下以不同用户账号对应的目录等3个地方。

关于这3个地方执行的区别，可以这样说

1. /etc/cron.d ---> 程序包使用，默认不带环境变量（下文详解）
2. /etc/crontab ---> 是系统cron的crontab，这样说不知道能不能理解？并且可以在里面指定具体执行的用户
3. /var/spool/cron/crontabs ---> 是具体用户的crontab

最好不要手动去修改 crontab文件，而使用 crontab -e 命令。

关于上面的具体，从man查询中，还有一个比较重要

> cron also reads /etc/crontab, which is in a slightly different format (see crontab(5)). Additionally, cron
reads the files in /etc/cron.d: it treats the files in /etc/cron.d as in the same way as the /etc/crontab
file (they follow the special format of that file, i.e. they include the user field). However, they are
independent of /etc/crontab: they do not, for example, inherit environment variable settings from it. The
intended purpose of this feature is to allow packages that require finer control of their scheduling than
the /etc/cron.{daily,weekly,monthly} directories to add a crontab file to /etc/cron.d.

翻译过来就是

> cron也会读取 /etc/crontab 的内容来执行，虽然格式上稍微有一些不同（我觉得就是多了用户名那一项）。除此之外，cron也会读取 /etc/cron.d 目录下的文件，并将其视做与 /etc/crontab 相对的方式对待。尽管如此，/etc/cron.d 与 /etc/crontab 相互独立，此话的意思就是，/etc/init.d 中的人物，不会提供环境变量，这样做的目的，是给予 /etc/init.d 目录下的程序包更好更精确的控制权。

这点区别很重要，如果你想查看当前用户下的环境变量都有哪些，使用 `env` 命令，然后如果你发现你的脚本在 /etc/crontab 下可以执行，但是到了 /etc/init.d/下就不行了，一般就是环境变量的问题。登录那个账户，然后把环境变量直接加到你的脚本里，一般就可以解决问题。

## Cron涉及到的文件

这里以REHL系统为例，其他系统大同小异，但是也会有细微差别，我在树莓派上的具体文件和区别标注在括号里。

- /usr/sbin/crond - 核心可执行文件（在树莓派下是cron）
- /etc/crontab - 系统cron的表，指定具体了任务的地方（树莓派相同）
- /usr/bin/crontab - 用来用来建立和管理cron表的入口可执行文件（树莓派相同）。
- /var/spool/cron/* - 不同用户创建的不同的cron任务（树莓派相同）。
- /etc/cron.d/* - 不同程序安装包创建的cron任务（树莓派相同）。
- /etc/cron.allow - 允许访问的记录
- /etc/cron.deny  - 禁止访问的记录
- /etc/cron.hourly/ - 这里的脚本，每个小时会执行一次。
- /etc/cron.daily/   - 这里的脚本，每天会执行一次。
- /etc/cron.weekly/ - 这里的脚本，每周会执行一次。
- /etc/cron.monthly/ - 这里的脚本，每个月会执行一次。

## Crontab的环境变量

cron会从用户登录时的HOME目录提取用户的默认环境变量加上默认的shell(/usr/bin/sh)。如果这个用户默认没shell，就没shell。

具体定义是：

``` bash
- HOME=<Users Home Dir>
- LOGNAME=<Users Login ID>
- PATH=/usr/bin:/usr/sbin:.
- SHELL=/usr/bin/sh
```

## cron使用图解

### 格式

``` bash
* * * * * <Command to execute>
```

不废话，直接上图

![crontab的语法格式](crontab.jpg)

### 编辑crontab

``` bash
crontab -e 
或者
crontab -u foolman -e  #root用户通过-u参数可以指定编辑具体用户的crontab文件
或者
crontab -u foolman foolman_cron.txt #直接导入任务
```

### 删除任务

``` bash
crontab -r #root用户或者其他用户都可以
或者
crontab -u foolman -r  #root用户删除某个用户的任务
```

### 列出任务

``` bash
crontab -l #root用户或者其他用户都可以
或者
crontab -u foolman -l  #root可以列出某用户的任务
```

### 启动或者停止cron服务

``` bash
service crond start
service crond stop
service crond restart
service crond status
```

上面是REHL系统的，如果是树莓派，因为没有crond，只有cron，所以命令要变成

``` bash
service cron start
service cron stop
service cron restart
service cron status
```

### 关闭邮件提示

默认情况下，如果cron遇到错误了，就会给你发一封邮件，默认保存在 /var/mail 下对应的用户名文件中。但是你可以通过在cron任务的默认，加上一句

``` bash
>/dev/null 2>&1
```

来取消邮件的发送。

### 生成cron日志

``` bash
10 10 * * * rm /home/foolman/tmp/* > /home/foolman/cronlogs/clean_tmp_dir.log
```

## 参考文章

1. [Linux Start Restart and Stop The Cron or Crond Service](http://www.cyberciti.biz/faq/howto-linux-unix-start-restart-cron/)
2. [Debian or Ubuntu Linux runlevel configuration tool to start service](http://www.cyberciti.biz/faq/howto-runlevel-configuration-tool-to-start-service/)
3. [/etc/crontab vs /etc/cron.d vs /var/spool/cron/crontabs/](http://www.linuxquestions.org/questions/linux-newbie-8/etc-crontab-vs-etc-cron-d-vs-var-spool-cron-crontabs-853881/)
4. [Working with "crontab" Scheduler](http://vlinux-freak.blogspot.com/2010/12/working-with-crontab-scheduler.html)

