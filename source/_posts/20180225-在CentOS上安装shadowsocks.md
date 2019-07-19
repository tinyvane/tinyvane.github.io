---
title: 在CentOS上安装shadowsocks
categories: CentOS
tags: [centos, shadowsocks]
date: 2018-02-25 20:15:34
urlname: use_shadowsocks_on_centos_as_ladder_to_cross_the_greatwall
---

## 文章更新

1. 20170330-初次成文
2. 20180929-更新

## 为什么会有这篇文章

之前翻墙都用搬瓦工，后来用了其他的几个VPS，都没有像搬瓦工那么方便的全自动的配置，所以就只好手动搭梯子了。

## 在CENTOS6上安装shadowsocks

### 安装pythong-pip

``` bash
sudo yum install python-pip
```

结果会显示

``` accesslog
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyuncs.com
 * epel: mirrors.aliyuncs.com
 * extras: mirrors.aliyuncs.com
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyuncs.com
Package python-pip-7.1.0-1.el6.noarch already installed and latest version
Nothing to do
```

上面是因为我已经安装过pip，所以显示nothing to do，如果首次安装， 结果会有所不同。

### 安装shadowsocks

继续使用pip安装shadowsocks

``` bash
sudo pip install shadowsocks
```

结果显示如下

``` accesslog
Collecting shadowsocks
  Downloading http://mirrors.aliyun.com/pypi/packages/02/1e/e3a5135255d06813aca6631da31768d44f63692480af3a1621818008eb4a/shadowsocks-2.8.2.tar.gz
Installing collected packages: shadowsocks
  Running setup.py install for shadowsocks
Successfully installed shadowsocks-2.8.2
```

### 建立配置文件

建立配置文件 `vim /etc/shadowsocks/config.json`

``` json
{
  "server":"xxx.xxx.xxx.xx", #可以使用的ss服务器IP
  "server_port":1080, #ss服务器端口
  "local_address": "127.0.0.1",
  "local_port":1080, #本地端口
  "password":"password", #连接ss服务器密码
  "timeout":600, #等待超时
  "method":"aes-256-cfb", #加密方式
}
```

### 运行shadowsocks服务

使用命令控制启动和关闭

``` bash
sslocal -c /etc/shadowsocks/config.json -d start
sslocal -c /etc/shadowsocks/config.json -d stop
```

检查是否成功启动

``` bash
netstat -lnp|grep 1080
```

加入开机自启动

``` bash
echo "nohup sslocal -c /etc/shadowsocks/config.json /dev/null 2>&1 &" /etc/rc.local
```
