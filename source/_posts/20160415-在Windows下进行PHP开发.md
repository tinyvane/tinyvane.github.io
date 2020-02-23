---
title: 在Windows下进行PHP开发
date: 2016-04-15 08:57:42
categories: 折腾
tags: [vscode, php, 开发]
urlname: php_development_on_windows
---

## 文章更新

1. 20160415-初次成文
2. 20160923-增加了XAMPP的配置内容

## 为什么会有这篇文章

之前零零散散的写过2-3篇文字，终于整合到了一起

## XAMPP配置

注意关闭WINDOWS自带的IIS服务，这个需要打开控制面板（WIN+X，选择控制面板），依次选择`程序`-`开启或关闭WINDOWS功能`，然后把`INTERNET INFORMATION SERVICE`服务前面的勾勾掉，然后再启动APACHE就不会报错了。

### hosts文件修改

在自己机器上开发，肯定不是一个PHP网站的，所以要建立多个网站的别名访问

先到WINDOWS的安装盘，依次进入`WINDOWS`-`SYSTEM32`-`DRIVERS`-`ETC`目录，找到`HOSTS`文件

基本上就是这样一个路径`C:\Windows\System32\drivers\etc`。

添加几行

``` json
127.0.0.1		xiaowei.com
127.0.0.1 		peipei.com
127.0.0.1 		meimei.com
```

### APACHE多域名

找到你的XMAPP目录，依次进入`apache`-`conf`-`extra`目录，编辑`httpd-vhosts.conf`文件。

添加下面这样几行

``` conf
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "D:/Sync Projects/php/peipei.com"
    ServerName peipei.com
    ErrorLog "logs/peipei.com-error.log"
    CustomLog "logs/peipei.com-access.log" common
	<Directory "D:/Sync Projects/php/peipei.com">
        # AllowOverride All      # Deprecated
        # Order Allow,Deny       # Deprecated
        # Allow from all         # Deprecated

        # --New way of doing it
        Require all granted    
    </Directory>
</VirtualHost>
```

然后在xmapp目录下的htdocs目录（这个是在apache.conf文件中定义的，你网站的默认位置路径），建立和上面的`peipei.com`想对应的目录即可。

另外，最近还有一个地方也最好一起修改了，就是在`xampp`-`apache`-`conf`下的httpd.conf文件中的默认htdocs目录，也修改一下权限，将`Require all deined`修改为`Require all granted`。

然后，打开chrome浏览器，输入http://peipei.com，就可以访问你本地的虚拟目录了，很方便。

## 安装Visual Studio Code

微软官方[下载地址](https://www.visualstudio.com/en-us/products/code-vs.aspx)

![下载页面](vscdownload.jpg)

下载安装后，然后设置autosave（自动保存）

![默认设置](vscautosave.jpg)

英文版VSC从`file`->`preference`->`user setting`里，用`Ctrl+F`找到的"files.autoSave": "off",

然后粘贴到右边，粘贴为"files.autoSave": "afterDelay"，保存，延迟时间可以自己修改。

![默认设置](vscautosave2.jpg)

## 安装PHP集成开发环境

[XAMPP下载地址](https://www.apachefriends.org/index.html)

我自己使用的XAMPP，装在了E盘根目录下。主要是因为C盘是一个128G的SSD，空间十分吃紧。

写这个记录的时候，版本是5.6.19。

![XAMPP启动界面](xampp.jpg)

如果80端口被占用，就使用8080端口，关于XAMPP的调试和错误，我回来打算另开一个帖子记录一下，并且MAC下就不打算用XAMPP了，而是用vagrant+box的组合，装个centos和自己的云服务器配置一样，因为云服务器确实装一次挺折腾的。后话不说。

## 为VS Code安装php开发环境

打开VSC，按ctrl+shift+p（这个快捷键和sublime的一样），输入`ext install php`，

我这里装的是中文版，可以直接看到安装扩展的选项。

可以看到php先关的扩展有不少，大家可以按照自己的需求安装。

![VSC安装扩展](vscextinst.jpg)

我主要装了debug和format。

然后vsc提示安装成功，重启vsc便可以启用相关扩展。

## 添加php调试参数

重启了vsc，打开一个Php,会看到vsc的一个提示。

![vsc提示缺少php配置](phpconifig1.jpg)

需要在用户设置里添加一个`php.validate.executablePath`，让php插件可以找到php.exe的路径。

按照上面找到`autosave`的方式，打开用户设置，在右侧添加一句

`"php.validate.executablePath": "E:/xampp/php/php.exe"`

记住如果仅有一条配置，最后一个json，不需要逗号。

不解释，直接上图。

![用户设置](usersetting.jpg)

## 下载xDebug

从打开中，选择`打开文件夹`，然后切换到DEBUG模式，通过点击绿色小箭头，会弹出一个提示，让你设置xDebug的参数。
这是因为vsc使用的php debug插件用到了xDebug，xDebug的下载地址和说明，见下文章节。

### 概念解释

#### 什么是xDebug

> Xdebug是一个开放源代码的PHP程序调试器(即一个Debug工具)，可以用来跟踪，调试和分析PHP程序的运行状况。Xdebug现在的最新版本是Xdebug 2.4.0RC4,release日期 2016-01-25，添加了对PHP7的支持。

所以，我们需要先花一点时间来下载和安装xDebug，[下载地址](https://xdebug.org/download.php)，有两个版本带有Non-thread-safe和不带有Non-thread-safe，主要是看你的php版本是否线程安全版本。Windows下需要下载非线程安全的32位版本。

上图说话。

![众多的XDEBUG下载选择让我眼花缭乱](xdebugdownload.jpg)

图片中第一个红框中，是按照你所安装的php.exe的版本选择，什么？你不记得你安装的php版本了？去你的集成开发环境包中看都集成的appache php和mysql分别是什么版本吧。什么？你也觉得费劲？那就来个更省事的，直接localhost/phpinfo.php来看吧，什么？你这个文件没了？那么，自己建立一个吧，什么？你不知道基本语法是什么？

我。。。

下面是一个最基本的phpinfo()。
``` php
<?php

// 显示所有信息，默认显示 INFO_ALL
phpinfo();

// Show just the module information. 仅仅显示PHP模块信息，
// phpinfo(8) 返回同样的结果。
phpinfo(INFO_MODULES);

?>
```

这个文件的格式，可以参考PHP的官方说明，地址在[这里](http://php.net/manual/zh/function.phpinfo.php)。创建一个文件phpinfo.php然后把上面的内容写进去，然后放在你的localhost路径下，浏览器中打开http://localhost/phpinfo.php，就可以看到执行效果了。

![通过phpinfo查看php版本信息](phpversion.jpg)

可以看到自己电脑上安装的php版本号是5.6。

> * PHP的大版本主要分支：PHP4/PHP5/PHP7(PHP6官方没有）

> 其中，PHP4由于太古老、对OO支持不力已基本被淘汰，请无视PHP4。

> PHP6/PHP7由于基本没有生产线上的应用，还基本只是一款概念产品，很多功能已在PHP5.3.3上实现，所以也不详述，请无视PHP6/7。

> PHP5的版本主要分四支：PHP5.2之前的版本、PHP5.2.X、PHP5.3和日前发布的PHP5.4。

> * 那我们应该如何选择适用自己项目的版本呢？

> PHP5.2之前的版本不值得考虑，因为某些功能缺陷或者BUG，PHP5.2之前的版本。PHP5.4还处于Beta试用的版本号，非稳定版本，请无视PHP5.4。

> 主流PHP程序对PHP5.2.X的兼容性最好，而每次版本号的升级带来的都是安全性和稳定性的改善，所以宜挑选最新的版本。目前PHP5.2系列最新的是PHP5.2.17。

> 而如果产品是自己开发自己使用，PHP5.3在某些方面更具优势，在稳定性上更胜一筹，增加了很多PHP5.2所不具有的功能，比如内置php-fpm、更完善的垃圾回收算法、命名空间的引        入、sqlite3的支持等等，是部署项目值得考虑的版本，强烈推荐PHP5.3.29。（这是5.3 的最后一个版本)

上面这段文件是转载别人博客上的，大家参考就好。

#### 如何选择VC9/VC11/VC14？

php 7.0对应vc14，php 5.5 和 5.6 对应vc11，php 5.4对应vc9。

#### TS是什么意思？

TS指Thread Safety，即线程安全，一般在IIS以ISAPI方式加载的时候选择这个版本。

NTS即None-Thread Safe，一般以fast cgi方式运行的时候选择这个版本，具有更好的性能。

> 从2000年10月20日发布的第一个Windows版的PHP3.0.17开始的都是线程安全的版本，这是由于与Linux/Unix系统是采用多进程的工作方式不同的是Windows系统是采用多线程的工作方式。如果在IIS下以CGI方式运行PHP会非常慢，这是由于CGI模式是建立在多进程的基础之上的，而非多线程。一般我们会把PHP配置成以ISAPI的方式来运行，ISAPI是多线程的方式，这样就快多了。但存在一个问题，很多常用的PHP扩展是以Linux/Unix的多进程思想来开发的，这些扩展在ISAPI的方式运行时就会出错搞垮IIS。因此在IIS下CGI模式才是 PHP运行的最安全方式，但CGI模式对于每个HTTP请求都需要重新加载和卸载整个PHP环境，其消耗是巨大的。

> 为了兼顾IIS下PHP的效率和安全，微软给出了FastCGI的解决方案。FastCGI可以让PHP的进程重复利用而不是每一个新的请求就重开一个进程。同时FastCGI也可以允许几个进程同时执行。这样既解决了CGI进程模式消耗太大的问题，又利用上了CGI进程模式不存在线程安全问题的优势。

> 因此，如果是使用ISAPI的方式来运行PHP就必须用Thread Safe(线程安全)的版本；而用FastCGI模式运行PHP的话就没有必要用线程安全检查了，用None Thread Safe(NTS，非线程安全)的版本能够更好的提高效率。

上面看完了，按照你的PHP版本，下载对应的xDebug文件，下载之后，是一个dll文件，把这个文件，复制到你的php集成开发环境的目录的位置。

我使用的XMAPP，放在了E盘，所以，我把这个dll文件复制到了e:\xmapp\php\etc目录下，这个目录是Php的拓展目录。

### 修改php.ini

在e:\xmapp\php目录下，找到你的php.ini文件，在文件的末尾，添加如下代码：

``` plain
[XDebug]
zend_extension = E:\xampp\php\ext\php_xdebug-2.4.0-5.6-vc11.dll
xdebug.remote_enable=1
xdebug.remote_autostart=1
```

这里，有几个需要注意的地方，先说`zend_extension`，如果你下载的是TS版本，这个参数需要改为`zend_extension_ts`。

并且，由于xDebug和Zend有冲突，所以请把zend的相关参数要注释掉。

典型就是如下几条：

``` plain
[Zend]
;zend_extension_ts = "C:\Program Files\xampp\php\zendOptimizer\lib\ZendExtensionManager.dll"
;zend_extension_manager.optimizer_ts = "C:\Program Files\xampplite\php\zendOptimizer\lib\Optimizer"
;zend_optimizer.enable_loader = 0
;zend_optimizer.optimization_level=15
;zend_optimizer.license_path =
```

我使用的XMAPP 2015年11月12日的版本，发现上述修改在我这里是不需要的，直接添加xDebug的三行代码，保存PHP.INI，然后回到XMAPP的控制面板，把APACHE先STOP然后再START，就可以了。

如何测试呢？用浏览器打开http://localhost/phpinfo.php

如果看到配置里面，有xDebug的项目，就说明配置成功了，最简单的就是直接CTRL+F搜索XDEBUG，没有就说明失败。

我在网上学习的过程中，找到了一篇非常有参考价值的博文，里面介绍了XDEBUG官方的一种"贴心"的配置php.ini的方式，博文原文地址在[这里](http://www.blogdaren.com/m/?post=2062)，我这里引用其中一段即可。

> 再次浏览 Xdebug 官网，发现有一个贴心的服务，就是：提取用户的 phpinfo 信息，提交给 Xdebug 官网的一个程序，它立即分析 PHP 环境的信息，即刻给出下载 Xdebug 某个版本的建议。立等可取，有点意思！

> 于是提交我的 phpinfo 信息，从 http://www.xdebug.org/find-binary.php 页面提交，得到提示信息：

下面用我自己返回的配置复制黏贴到上文中的[官方地址](http://www.xdebug.org/find-binary.php )。

![XDEBUG官方给出的答复](xdebugofficialdiag.jpg)

然后就看到了官方给出的建议：

![XDEBUG官方给出的答复](xdebugofficialanswer.jpg)

按照这个指导，圆满的完成了我的xDebug配置，真是非常贴心！这个办法，对于开发者也非常给力。

下面继续回到VSCode。

### 修改VSC DEBUG设置

直接上图

![VSC中配置DEBUG模式](debuginvsc.jpg)

1. 点左侧的DEBUG按钮（虫子那个）
2. 在绿色箭头右侧，选择LISTEN FOR XDEBUG
3. 在右侧你的PHP的代码上，随便某一行打上个断点
4. 点F5，会看到VSC编辑器上方，出现了熟悉的"继续"、"单步跳过"等微软风格的调试按钮。
5. 然后，请到你的浏览器里面，输入http://localhost，或者http://127.0.0.1进行调试你在VSC中打了断点的php程序。
6. 你会发现，浏览器直接切换回到VSC，并且停留在了你的断点位置。
7. GAME OVER~

再上个图

![VSC中的DEBUG断点](breakpointinvsc.jpg)

好了，终于搞完了WINDOWS下的PHP环境搭建，有啥问题或者博客中的问题，麻烦小伙伴直接恢复吧，有问必回，下班~

## 参考资料

1. [PHP与VC对应关系、TS和NTS的区别](http://www.bubuko.com/infodetail-1331103.html)
2. [xDebug百度百科词条](http://baike.baidu.com/view/1823486.htm)
3. [NETBEANS官方出的XMAPP配置XDEBUG说明](https://netbeans.org/kb/docs/php/configure-php-environment-windows_zh_CN.html)
4. [这里提供了XDEBUG官方一种贴心的配置方法](http://www.blogdaren.com/m/?post=2062)