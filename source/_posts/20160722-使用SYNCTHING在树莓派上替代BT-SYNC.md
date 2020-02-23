---
title: 使用SYNCTHING在树莓派上替代BT SYNC
date: 2016-07-22 09:18:30
categories: 树莓派
tags: [raspberry pi, syncthing, 同步, bt sync]
urlname: use_syncthing_on_raspberry_pi_to_replace_btsync
---

## 文章更新

1. 20160722-初次成文
2. 20160724-因为BTSYNC可能找到了错误原因，停止使用SYNCTHING

## 为什么会有这篇文章

好久没更新文章了，主要是之前工作上的事情太忙了，要翻译，又要写稿子，忙的时候都没有空去群里看大家聊天了。不过我又回来了，发现之前的命令开始模糊了，果然什么事情要熟练，都需要不断的反复练习和记忆，直到那些技能成为你身体的一部分。

好了，不废话了。~~BT SYCN在我的树莓派上一直不是很稳定，主要是外接硬盘一直不稳定排除了硬盘盒和硬盘的问题，目前也找不到比较好的解决办法，又懒得重新装一次树莓派系统和应用，所以先用SYNCTHING来感受一下是否稳定。~~

最近发现因为SAMBA服务可能和BTSYNC在某个地方冲突了，导致BTSYNC运行又正常了。<!-- more -->

## 安装和下载

``` bash
wget https://github.com/syncthing/syncthing/releases/download/v0.14.0/syncthing-linux-arm-v0.14.0.tar.gz
tar -zxvf syncthing-linux-arm-v0.14.0.tar.gz /usr/local/sbin/
```

然后

运行就一句话

``` bash
/usr/local/sbin/syncthing
```

然后看到一堆输出信息，告知syncthing在监听什么端口，NAT信息之类的。

使用 `CTRL+C` 来停止运行。

## 编辑配置文件

先来看看他的配置文件，

``` bash
sudo vim /home/pi/.config/syncthing/config.xml
```

``` bash
<gui enabled="true" tls="false">
 <address>0.0.0.0:8384</address>
 <apikey>VbsKT2fCELYldTI74Tk4BKCbJP8Frlij</apikey>
</gui>
```

这里需要改的地方，主要是address，修改为 `0.0.0.0:8384`，这样就可以外网访问了，并且如果有https连接，需要把tls改为true。

如果是外网访问，就可以通过 `http://your_pi_address:8384` 来访问了，不过记得在防火墙上做一个 `TCP 8384` 的端口转发，否则也是打不开 GUI 界面的。

可以访问了之后，第一次运行，会让你设置用户名和密码，不要设置 admin 这样太好猜的用户名，否则也不安全。

## 设置syncthing自动启动

在 `/etc/init.d` 目录下建立启动文件

``` bash
sudo vim /etc/init.d/syncthing
```

脚本内容如下

``` bash
#!/bin/sh
### BEGIN INIT INFO
# Provides:          Syncthing
# Required-Start:    $local_fs $remote_fs $network
# Required-Stop:     $local_fs $remote_fs $network
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Syncthing
# Description:       Syncthing is for backups
### END INIT INFO
 
# Documentation available at
# http://refspecs.linuxfoundation.org/LSB_3.1.0/LSB-Core-generic/LSB-Core-generic/iniscrptfunc.html
# Debian provides some extra functions though
. /lib/lsb/init-functions
 
DAEMON_NAME="syncthing"
DAEMON_USER=pi
DAEMON_PATH="/usr/local/sbin/syncthing-linux-arm-v0.14.0/syncthing"
DAEMON_OPTS=""
DAEMON_PWD="${PWD}"
DAEMON_DESC=$(get_lsb_header_val $0 "Short-Description")
DAEMON_PID="/var/run/${DAEMON_NAME}.pid"
DAEMON_NICE=0
DAEMON_LOG='/var/log/syncthing'
 
[ -r "/etc/default/${DAEMON_NAME}" ] && . "/etc/default/${DAEMON_NAME}"
 
do_start() {
  local result
 
	pidofproc -p "${DAEMON_PID}" "${DAEMON_PATH}" > /dev/null
	if [ $? -eq 0 ]; then
		log_warning_msg "${DAEMON_NAME} is already started"
		result=0
	else
		log_daemon_msg "Starting ${DAEMON_DESC}" "${DAEMON_NAME}"
		touch "${DAEMON_LOG}"
		chown $DAEMON_USER "${DAEMON_LOG}"
		chmod u+rw "${DAEMON_LOG}"
		if [ -z "${DAEMON_USER}" ]; then
			start-stop-daemon --start --quiet --oknodo --background \
				--nicelevel $DAEMON_NICE \
				--chdir "${DAEMON_PWD}" \
				--pidfile "${DAEMON_PID}" --make-pidfile \
				--exec "${DAEMON_PATH}" -- $DAEMON_OPTS
			result=$?
		else
			start-stop-daemon --start --quiet --oknodo --background \
				--nicelevel $DAEMON_NICE \
				--chdir "${DAEMON_PWD}" \
				--pidfile "${DAEMON_PID}" --make-pidfile \
				--chuid "${DAEMON_USER}" \
				--exec "${DAEMON_PATH}" -- $DAEMON_OPTS
			result=$?
		fi
		log_end_msg $result
	fi
	return $result
}
 
do_stop() {
	local result
 
	pidofproc -p "${DAEMON_PID}" "${DAEMON_PATH}" > /dev/null
	if [ $? -ne 0 ]; then
		log_warning_msg "${DAEMON_NAME} is not started"
		result=0
	else
		log_daemon_msg "Stopping ${DAEMON_DESC}" "${DAEMON_NAME}"
		killproc -p "${DAEMON_PID}" "${DAEMON_PATH}"
		result=$?
		log_end_msg $result
		rm "${DAEMON_PID}"
	fi
	return $result
}
 
do_restart() {
	local result
	do_stop
	result=$?
	if [ $result = 0 ]; then
		do_start
		result=$?
	fi
	return $result
}
 
do_status() {
	local result
	status_of_proc -p "${DAEMON_PID}" "${DAEMON_PATH}" "${DAEMON_NAME}"
	result=$?
	return $result
}
 
do_usage() {
	echo $"Usage: $0 {start | stop | restart | status}"
	exit 1
}
 
case "$1" in
start)   do_start;   exit $? ;;
stop)    do_stop;    exit $? ;;
restart) do_restart; exit $? ;;
status)  do_status;  exit $? ;;
*)       do_usage;   exit  1 ;;
esac
```

然后是两句

``` bash
sudo chmod +x /etc/init.d/syncthing #加可执行权限
sudo update-rc.d syncthing defaults #添加默认启动
```

如何要取消，一种是图形化的管理界面（略），另外一种则是

``` bash
sudo update-rc.d syncthing remove
```

然后你就有了4句命令

``` bash
sudo service syncthing start
sudo service syncthing stop
sudo service syncthing restart
sudo service syncthing status
```

## 一些尝试

已经共享的文件夹，即便删掉了共享，文件也依然会存在，我觉得这个功能很贴心，毕竟谁也不想自己辛辛苦苦共享的文件夹因为对方或者自己删除了共享，就真没了呢。不过BTSYNC被删掉或者共享失败的文件，会存在类型 btsync/iphone/.sync 这样的隐藏目录下面，不知道SYNCTHING有没有这个机制。

## 参考文章

1. [Install Syncthing Raspberry Pi – BitTorrent Sync Alternative](http://www.htpcguides.com/install-syncthing-raspberry-pi-bittorrent-sync-alternative/)
