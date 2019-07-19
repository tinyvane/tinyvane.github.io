---
title: MAC系统从BASH转到了OH-MY-ZSH
date: 2016-06-01 15:00:35
categories: 折腾
tags: [mac, oh-my-zsh, bash]
urlname: switch_from_bash_to_ohmyzsh
---

## 从bash转向zsh

MAC下的terminal不是很漂亮，换用了iTerm 2，正好今天iterm从2.1.4升级到了最新的3.0，

Zsh和bash一样，是一种Unix shell，但大多数Linux发行版都默认使用bash shell。但Zsh有强大的自动补全参数和自定义配置功能等等，如果想配置zsh，可以借助一个叫Oh-my-zsh的项目，来帮你轻松搞定，[Github地址](https://github.com/robbyrussell/oh-my-zsh)。
这里正好记录一下zsh的配置过程，下次如果重新装，也可以有个参考。

![](oh-my-zsh.png)

直接上代码。

### 安装oh-my-zsh

自动安装oh-my-zsh：

``` bash
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

或者手动安装oh-my-zsh：

``` bash
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

### 创建或修改zsh的配置文件

如果你已经有一个~/.zshrc文件的话，使用以下命令先做个备份也是好的

``` bash
cp ~/.zshrc ~/.zshrc.orig
```

然后从模板来创建zsh的配置文件

``` bash
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

### 设置zsh为你的默认的shell

``` bash
chsh -s /bin/zsh
```

如果你想切换回来bash，使用下列命令即可。

``` bash
chsh -s /bin/bash
```

上述过程都需要输入你的密码。如果你想看看自己的机子上装了哪些shell，可以使用如下命令：

``` bash
cat /etc/shells
```

### 重启ITERM或者TERMINAL启用新shell

重启并开始使用你的zsh（或者bash），简单的方式是直接打开一个新的终端窗口便可…

至此，GAME OVER.

## 关于插件

配置文件中找到 ```plugins=(git)```，想加什么插件就把名字放里面就是了，比如plugins=(rails git ruby) 就开启了rails，git 和 ruby 三个插件。

更多的介绍，大家可以去这个[地方](https://segmentfault.com/a/1190000002658335)，他写得已经很详细了，没有必要再引用过来重新写一遍了。

自己使用了git colorize 还有 web-search 这几个插件。

## 参考资料

[zsh – 给你的Mac不同体验的Terminal！](http://leeiio.me/bash-to-zsh-for-mac/)
[那些我希望在一开始使用 Zsh(oh-my-zsh) 时就知道的](https://segmentfault.com/a/1190000002658335)