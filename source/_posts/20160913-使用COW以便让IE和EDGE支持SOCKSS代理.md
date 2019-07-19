---
title: 使用COW以便让IE和EDGE支持SOCKSS代理
date: 2016-09-13 01:53:18
categories: 折腾
tags: [cow, socks5, shadowsocks, ie, edge]
urlname: use_cow_under_windows_10_to_make_ie_and_edge_to_support_socks5
---

## 文章更新

1. 201609012-初次成文1

## 为什么会有这篇文章

突然想用GOOGLE PHOTOS，下载了GOOGLE PHOTOS BACKUP，但是却发现无法登录。经过搜索发现，GOOGLE PHOTO使用的IE或者EDGE的代理通道，也就是说，如果IE无法翻墙，那么GOOGLE PHOTOS就用不了。我使用的是SHADOW SOCKS QT5的版本，在使用SHADOWS SOCKS代理的时候，只能在HTTP和SOCKS5两者二选一，如果选了HTTP，那么在CHROME下使用HTTP没有SOCKS5那么流畅，所以需要寻找一种方式，让IE和EDGE走HTTP，让CHROME继续使用SOCKS5通道，两全其美。而COW就是这个完美的解决方案。<!-- more -->

## 如何使用COW

### 需要准备的

1. shadow socks服务器，我是在搬瓦工处购买的20刀一年的服务器，一个月500G流量，足够用了。
2. cow客户端，下载地址https://github.com/cyfdecyf/cow

## cow的设置

windows版本，配置文件是那个rc.txt，我曾经也觉得奇怪，确实有点奇葩的配置文件名字，竟然是txt的扩展名。

rc.txt就需要两句，其他都没用。

``` ini
listen = http://127.0.0.1:7777 
proxy = ss://aes-256-cfb:PASSWORD@ss_server_ip.com:8388
```

## Windows IE和EDGE的选项设置

连接选项卡 -> 局域网设置 -> 勾选了(自动检测设置，使用自动配置脚本: http://127.0.0.1:7777/pac) -> 为LAN使用代理服务器 -> 高级 -> (HTTP/安全 127.0.0.1 : 7777)

然后，IE和EDGE就可以翻墙了，GAME OVER。

## 参考文章

1. [cow 到底怎么用的？](http://zqscm.qiniucdn.com/data/20141110114854/index.html)
2. [COW项目地址](https://github.com/cyfdecyf/cow)