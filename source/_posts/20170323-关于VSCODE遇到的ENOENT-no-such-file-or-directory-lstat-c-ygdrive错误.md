---
title: 关于VSCODE遇到的ENOENT-no-such-file-or-directory-lstat-c-ygdrive错误
date: 2017-03-23 13:23:24
categories: 折腾
tags: [vscode, git, mklink]
urlname: how_to_deal_with_vscode_and_git_from_cygwin
---

## 文章更新

1. 20170312-初次成文

## 为什么会有这篇文章

这是我从git for windows全面转向使用cygwin之后遇到的一个错误，有几天了，一直没有仔细检查错误的原因。今天终于抽空解决了一下。

## 错误描述

当你安装了cygwin，并且没有独立安装git for windows的时，而是使用了cygwin下的git插件，使用VSCODE打开一个git管理的项目，就会遇到一个错误`ENOENT: no such file or directory, lstat 'C:\cygdrive'`。

## 解决办法

* 安装git for windows
* 关于VSCODE中的git集成工程
* 使用软连接让vscode正确找到cygwin下的git插件（本文方法）

## 建立软连接

其实很简单，Windows下按WIN键，输入`cmd`，然后鼠标右键，选择以管理员身份打开

![管理员身份打开CMD](administratorcmd.png)

然后如果看到打开的命令行窗口左上角显示管理员即可

![管理员身份打开CMD](administratorcmd2.png)

然后输入

``` bash
mklink /j "C:\cygdrive" C:\cygwin64\home\tinyv
```

然后重启vscode，点击左侧的git图标，如果发现还出现错误，并且错误是`ENOENT: no such file or directory, lstat 'D:\cygdrive\c'`

则还需要下一步

进入你刚刚建立的c:/cygdrive，命令

``` bash
cd c:/cygdrive
```

然后输入命令

``` bash
mklink /j "C:\cygdrive\d" D:\
```

重启vscode，错误应该就好了。

## 几个说明

第一步中的tinyv，是你在`c:/cygwin64/home`目录下的用户名，这个要改成你自己的具体名字。

第二步中的`D:\`，是你用VSCode打开的项目所在的盘符，我的项目在d盘下，所以这里的错误提示是`/cygdrive/d`，这个也要修改成具体的盘符，而不能生搬硬套我的写法。

## 参考文章

1. [Keep getting error ENOENT: no such file or directory, lstat 'w:\cygdrive'](https://github.com/Microsoft/vscode/issues/1982)
2. [Visual Studio Code cannot detect cygwin git.exe path](http://stackoverflow.com/questions/36508177/visual-studio-code-cannot-detect-cygwin-git-exe-path/37980938#37980938)