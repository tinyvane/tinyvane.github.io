---
title: MAC系统下卸载NODE.JS并且使用BREW安装
date: 2016-07-03 22:58:00
categories: 折腾
tags: [homebrew, brew, node.js, mac, 安装, npm]
urlname: how_to_uninstall_nodejs_and_reinstall_with_brew
---

## 文章更新

1. 20160703-初次成文
2. 20170727-小错误更新

## 为什么会有这篇文章

主要是因为懒得用vagrant来配置，并且自己的电脑硬盘不是特别大，所以就想熟悉下，正好homebrew不熟悉，通过这次的练习，也算初步掌握了Homebrew的常见问题。<!-- more -->

## Mac下如何完整卸载node.js

因为之前使用brew doctor发现了node安装在了`/usr/local/include`目录下，这样homebrew就会觉得有文件没有应该放对位置，因此我就要先卸载一次再使用brew来安装它，而不是简单使用node.js的安装包安装。

``` accesslog
Warning: Unbrewed header files were found in /usr/local/include.
If you didn't put them there on purpose they could cause problems when
building Homebrew formulae, and may need to be deleted.

Unexpected header files:
    /usr/local/include/node/android-ifaddrs.h
```

能看到没有放对位置的文件，基本上都是Node.js的文件，因此，还是决定把这些目录全部删掉，网上搜了完整删除Node.js的方法，基本上就是吧Node.js的文件和目录全部删掉即可。

写了个脚本，也不算脚本吧。

``` bash
sudo rm -rf /usr/local/lib/node_modules
sudo rm -rf /usr/local/include/node
sudo rm -rf /usr/local/bin/node
sudo rm /usr/local/bin/npm
sudo rm /usr/local/share/man/man1/node.1
sudo rm /usr/local/lib/dtrace/node.d
sudo rm -rf ~/.npm
sudo rm -rf ~/.node-gyp
sudo rm /opt/local/bin/node #我这里没有找到
sudo rm /opt/local/include/node #我这里没有找到
sudo rm -rf /opt/local/lib/node_modules #我这里没有找到
```

好了，世界清静了。

## 使用homebrew安装Node.js和npm

### 确认Homebrew就绪

Homebrew的安装可以参见另外一个[帖子](http://www.wuliaole.com/post/the_tutorial_101_of_homebrew_on_mac)

### 安装Node.js和NPM

``` bash
brew install node
```

这样安装Node.js已经把NPM一起安装了，然后通过 `node -v` 和 `npm -v` 分别检查node和npm是否安装正确。

也可能会看到下面这个错误: node没有被link，使用 `brew link node`，遇到错误

``` accesslog
Linking /usr/local/Cellar/node/6.2.2...
Error: Could not symlink share/doc/node/gdbinit
Target /usr/local/share/doc/node/gdbinit
already exists. You may want to remove it:
  rm '/usr/local/share/doc/node/gdbinit'

To force the link and overwrite all conflicting files:
  brew link --overwrite node

To list all files that would be deleted:
  brew link --overwrite --dry-run node
```

上面的结果是说link node的时候遇到错误，建议先使用

1. `rm '/usr/local/share/doc/node/gdbinit'` #删除文件，然后重新使用
2. `brew link --overwrite node` #强制连接，并且覆盖所有冲突的文件

结果

``` accesslog
Linking /usr/local/Cellar/node/6.2.2... 7 symlinks created
```

连接成功了

再次 `node -v` 和 `npm -v` 如果能看到版本号，就说明node安装完毕。

## 参考文献

1. [How to Install Node.js and NPM on a Mac](http://blog.teamtreehouse.com/install-node-js-npm-mac)
