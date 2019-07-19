---
title: 如何让SHADOWSOCKS在MAC上自动启动
date: 2016-07-24 23:48:37
categories: 折腾
tags: [mac, launchagents, shadowsocks]
urlname: how_to_setup_automatic_load_for_shadowsocks_on_mac
---

## 文章更新

1. 20160724-初次成文

## 为什么会有这篇文章

因为发现每次使用shadowsocks的客户端的时候，都需要自己启动，系统不会自动启动。后来网上找了找，原来是使用launchagent这个目录来加载自动启动，就像lantern和php自动启动那种效果。<!-- more -->

## 什么是LaunchD

如果在mac上登录了自己的账户，使用iterm登录，利用 `cd ~\LaunchAgnents`，然后 ls，你会看到一些xml文件，比如我的系统上，是这样的

以lantern为例，我来看看这里都有什么内容：

``` bash
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN"
		"http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
		<key>Label</key>
		<string>org.getlantern</string>
		<key>ProgramArguments</key>
		<array>
		<string>/Applications/Lantern.app/Contents/MacOS/lantern</string>
		<string>-startup</string>
		</array>
		<key>RunAtLoad</key>
        <true/>
	</dict>1
	</plist>
```

很简单明白吧，就是自动加载 `/Applications/Lantern.app/Contents/MacOS/lantern` 这个位置上的lantern程序，并且将其设置为 RunAtLoad 模式。

如果大家想学习更多关于Lanunch的语法，详细的参数配置可以参考 [launchd.info](http://http://launchd.info/) 这个教程。我仿照这个格式，写了一个自己启动 shadowsocks的文件。

``` bash
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN"
		"http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
		<key>Label</key>
		<string>shadowsocks</string>
		<key>ProgramArguments</key>
		<array>
		<string>/Applications/ShadowsocksX.app/Contents/MacOS/ShadowsocksX</string>
		<string>-startup</string>
		</array>
		<key>RunAtLoad</key>
        <true/>
	</dict>
	</plist>
```

将这个配置文件保存为 `~/Library/LaunchAgents/shadowsocks.plist`， 然后使用下面命令加载：

``` bash
launchctl load ~/Library/LaunchAgents/shadowsocks.plist
```

由于配置了 `RunAtLoad` 参数，所以加载后 Shadowsocks 就会运行。 如果想更方便一些，可以在配置文件里指定的 `shadowsocks.stdout` 和 `shadowsocks.stderr` 里看到运行日志。

如果想要停止服务，只需要 'unload' 这个配置即可：

``` bash
launchctl unload ~/Library/LaunchAgents/shadowsocks.plist
```

## 参考文章

1. [Mac OS 中使用 launchd 自动启动 Shadowsocks](http://gnailuy.com/mac/2015/05/19/launchd-configuration-for-shadowsocks/)
