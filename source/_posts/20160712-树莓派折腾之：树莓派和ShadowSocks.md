---
title: 树莓派折腾之：树莓派和ShadowSocks
date: 2016-07-12 22:28:10
categories: 树莓派
tags: [raspberry, 树莓派, shadowsocks]
urlname: raspberry_pi_and_shadowsocks
---

## 文章更新

1. 20160712-初次成文
2. 20160907-CENTOS安装方法

## 为什么会有这篇文章

因为发现在树莓派系统中，使用wget下载OPENVPN源代码的时候，远程服务器竟然连不上，估计是被QIANG掉了，所以，只能在树莓派上安装ShadowSocks的客户端了，用来让树莓派访问那些被功夫网鄙视的网站了。<!-- more -->

## 树莓派安装Shadowsocks客户端

先保证你的QIANG外服务器上安装好了Shadowsocks的服务器端，如何安装可以参考我的另外一篇文章：《[搬瓦工自动配置shadowsocks翻墙](http://www.wuliaole.com/post/how_to_scientificly_online_by_shadowsocks)》，记下服务器的IP、端口号、密码以及加密方式。

接下来, 在树莓派上配置Shadowsocks客户端, 这是让树莓派翻QIANG的必要条件。

``` bash
#安装pip管理python包
sudo apt-get install python-pip python-m2crypto
#安装python版shadowsocks
sudo pip install shadowsocks
#设置shadowsocks客户端配置文件
sudo vim /etc/shadowsocks.json
```

`shadowsocks.json`文件内容模板如下：

``` json
{
  "server":"xxx.xxx.xxx.xxx", //VPS IP
  "server_port":8388, //VPS端口
  "local_address": "xxx.xxx.xxx.xxx", //树莓派IP
  "local_port":1080, //树莓派端口
  "password":"mypassword", //VPS上设置shadowsocks服务器的密码
  "timeout":60,
  "method":"encrypt_method", //VPS 上设置的加密方式, 不知道可以选择"aes-256-cfb"
  "fast_open": false,
  "workers": 1
}
```

若要设置SS客户端开机自启动，需编辑`/etc/rc.local`文件，在最后的`exit`一行之前添加：

``` bash
/usr/local/bin/sslocal -c /etc/shadowsocks.json -d start
```

然后检查sslocal是否正确运行

``` bash
sudo systemctl status rc-local.service
```

如果`rc.local`正确运行，可以看到类似下面的的输出：

``` accesslog
● rc-local.service - /etc/rc.local Compatibility
   Loaded: loaded (/lib/systemd/system/rc-local.service; static)
  Drop-In: /etc/systemd/system/rc-local.service.d
           └─ttyoutput.conf
   Active: active (running) since Wed 2016-07-13 00:06:01 CST; 1min 40s ago
  Process: 603 ExecStart=/etc/rc.local start (code=exited, status=0/SUCCESS)
 Main PID: 1072 (sslocal)
   CGroup: /system.slice/rc-local.service
           └─1072 /usr/bin/python /usr/local/bin/sslocal -c /etc/shadowsocks.json -d start
```

## curl和wget实例

具体如何让curl和wget走代理呢？很简单

``` bash
curl -x http://127.0.0.1:1080 github.com
wget -e "http_proxy=127.0.0.1:1080" github.com
```

GAME OVER.

## 参考文章

1. [在树莓派上设置透明代理](https://lttt.blog.ustc.edu.cn/2015/06/11/shadowsocks%E6%A0%91%E8%8E%93%E6%B4%BE%E7%BF%BB%E5%A2%99.html)