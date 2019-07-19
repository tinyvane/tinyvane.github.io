---
title: 搬瓦工自动配置shadowsocks翻墙
date: 2016-07-08 00:36:44
categories: 折腾
tags: [搬瓦工, shadowsocks, 科学上网]
urlname: how_to_scientificly_online_by_shadowsocks
---

## 文章更新

1. 20160708-初次成文

## 为什么会有这篇文章

便宜买了个搬瓦工的VPS，就是为了能科学上网用，看到在主机的控制面板里，竟然有一键配置，觉得搬瓦工还是很人性化的，能简单的地方就不要复杂嘛。<!-- more -->

## 一键安装shadowsocks

搬瓦工自带shaowsocks一键安装，直接进入KiwiVM控制面板，拉倒最下面的Shadowsocks Server，安装就好。然后系统就会分配IP、端口和密码，如果自己用，直接使用就行了。

## 配置说明

自带的系统只有一个用户，想自己多弄几个用户，就要自己更改配置。SS官方的WIKI上，也公布了[单用户配置](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)，和[多用户配置](https://github.com/shadowsocks/shadowsocks/wiki/Configure-Multiple-Users)两个具体配置文件。

``` bash
vim /etc/shadowsocks.json
```

我使用的多用户配置，

``` bash
{
 "server":"my_server_ip",
 "local_address": "127.0.0.1",
 "local_port":1080,
  "port_password": {
     "8381": "foobar1",
     "8382": "foobar2",
     "8383": "foobar3",
     "8384": "foobar4"
 },
 "timeout":300,
 "method":"aes-256-cfb",
 "fast_open": false
}
```

配置的说明：
server			the address your server listens（服务器IP）
local_address	the address your local listens（本地代理地址）
local_port		local port（本地代理端口）
port_password	password used for encryption(自己设定的服务器端口和密码)
timeout			in seconds（超时断开，以秒为单位）
method			default: "aes-256-cfb", see Encryption（加密方式）
fast_open		use TCP_FASTOPEN, true / false（是否使用TCP）
workers			number of workers, available on Unix/Linux（这个只在Unix和Linux下有用，可不设置）

## SS的启动

可选择前端启动（可看见日志），或者后台启动

前端启动：

ssserver -c /etc/shadowsocks.json

后端启动：

开始：ssserver -c /etc/shadowsocks.json -d start
结束：ssserver -c /etc/shadowsocks.json -d stop

## 设置开机启动

设置好了，但是如果只是这样，那每次都要手动启动ss，太麻烦。可以将其加到开机启动项。

``` bash
vim /etc/rc.local
```

将最后一行删掉，那是官方的默认配置，然后添加一行

``` bash
ssserver -c /etc/shadowsocks.json -d start
```

## 其他说明

非root用户运行ss

按照上面的设置shadowsocks是以root权限运行的，不是很安全，可以这样设置。

sudo useradd ssuser //添加一个ssuser用户

sudo ssserver [other options] --user ssuser //用ssuser这个用户来运行ss

其中的[other options]是只，之前启动ss的命令，比如ssserver -c /etc/shadowsocks.json -d start。这样就可以使用非root用户来运行ss了。

然后修改开机启动项，将之前的ssserver -c /etc/shadowsocks.json -d start改为ssserver -c /etc/shadowsocks.json -d start --user ssuser，然后保存就OK了。

更多的问题，请看官方的说明文档。

## 参考文章

1. [搬瓦工Shadowsocks配置总结](http://www.jianshu.com/p/36e55c289d65)

