---
title: 20190719-shadowsocks支持windows邮件、OFFICE登陆翻墙
categories: 折腾
tags:
  - git
date: 2019-07-19 13:11:23
urlname: let_windows_mail_office_login_using_shadowsocks
---

## 为什么会有这篇文章

即便使用了SSR开了GLOBAL模式，会发现自己的APP STORE和OFFICE 登陆等界面，还是无法打开页面。

## 前置条件

windows10、ssr

## 正文

使用admin模式打开powshell，一条命令搞定。

``` bash
foreach($f in Get-ChildItem $env:LOCALAPPDATA\Packages) {CheckNetIsolation.exe LoopbackExempt -a "-n=$($f.Name)"}
```

或者使用下面的脚本

``` bat
@echo OFF
echo 清楚所有程序 && CheckNetIsolation.exe LoopbackExempt -c 1>nul
cd %USERPROFILE%\AppData\Local\Packages
echo 开始添加程序
for /d %%i in (*) do echo 添加程序%%i && CheckNetIsolation.exe LoopbackExempt -a -n = %%i 1>nul
pause
```

### 标题1

### 标题2

## 参考链接

1. [参考链接](https://github.com/shadowsocks/shadowsocks-windows/issues/897)
2. [参考链接](http://www.wuliaole.com)
3. [参考链接](http://www.wuliaole.com)
