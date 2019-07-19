---
title: OSX下配置VSCode的PHP调试环境
date: 2016-06-20 12:33:31
categories: 折腾
tags: [vscode, php, mac, xdebug]
urlname: config_php_debug_environment_on_mac_osx_with_vscode
---

## 文章更新

1. 20160620-初次成文，基本照搬了WIN下的设置，文章比较杂乱，没有图
2. 20160823-补充图片，更新内容
3. 20170727-查漏补缺

## 为什么会有这篇文章

因为自己在MAC下不能写WinForm的程序，所以用来练习PHP比较顺手，而正好也在接触ThinkPHP框架，就在MAC下重温一次安装和代码调试过程。

PS: Windows系统下的PHP环境配置，请戳[这里](http://www.wuliaole.com/post/php_development_on_windows)<!-- more -->

## 在OS X下配置PHP环境

请看我的另外一个帖子[《MAC OS系统使用Homebrew配置NGINX PHP和MYSQL》](http://www.wuliaole.com/post/config_nginx_php_and_mysql_on_mac_using_homebrew)，环境包括安装PHP 5.6，NGINX, MYSQL

## 下载并安装VSCode

VSCode[下载地址](https://code.visualstudio.com/)，选择OSX版本下载。

下载安装后，双击解压缩，最新版本的VSCode会自动提示是否要把程序移动到Application文件夹以保持下载文件夹的干净，同意。

小技巧，运行VSCode之后，按 `F1` 键，输入命令 `Shell命令`，提能提示后选择 `Shell命令：在PATH中安装 code 命令`，这样处理之后，在shell下的任意地址都可以通过输入 `code .` 命令打开VSCode并把当前目录作为工作目录。

打开程序后，依次选择 `CODE` `首选项` `用户设置`，从左侧把这样几个设置复制到右侧去，然后做相应的修改。

``` json
// 将设置放入此文件中以覆盖默认设置
{
    "editor.fontFamily": "Monaco, 'Courier New', monospace",
    "editor.fontSize": 14,
    "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 1000,
    //下面这句是指出Php的安装路径，为了XDEBUG调试用的
    "php.validate.executablePath": "\usr\local\sbin\php56" 
}
```

## 在VSCode安装php调试插件

打开VSC，按Command+shift+p（这个快捷键和sublime的一样），输入`ext install php debug`，回车后会在VSCODE STORE里搜索 `php debug`为关键词的组件，我装了PHP DEBUG，版本是1.9.4

我主要装了debug和format。

然后vsc提示安装成功，重启vsc便可以启用相关扩展。

## 添加php调试参数

重启了vsc，打开一个Php,会看到vsc的一个提示。

> 这里说点题外话，在osx下，用finder默认是看不到像 /var /tmp 这些隐藏文件夹的，如果你希望在finder中看到这些目录，请在terminal下执行 `defaults write com.apple.Finder AppleShowAllFiles YES`，然后按住Option键，单击Dock上的Finder图标不放，大概2秒后将在Finder图标上打开菜单，最下面多了一行"重新启动"，单击启动FINDER就可以看到隐藏的目录和文件了。想关闭也简单，YES改成NO再执行一次就好了。

![vsc提示缺少php配置](phpconifig1.jpg)

需要在用户设置里添加一条配置 `php.validate.executablePath`，让php插件可以找到php的路径。

按照上面添加的配置，找到`autosave`的方式，打开用户设置，在右侧添加一句

``` json
"php.validate.executablePath": "\usr\local\sbin\php56"
```

记住如果仅有一条配置，最后一条json配置，末尾没有逗号。

不解释，直接上图。

![用户设置](usersetting.jpg)

## 安装xDebug

在MAC系统下，很多程序都可以使用Homebrew来安装，xDebug也不例外。

命令是

``` bash
brew tap josegonzalez/php
brew tap homebrew/dupes
brew tap homebrew/versions
brew tap homebrew/homebrew-php
brew install php56-xdebug
```

因为xDebug默认同样使用 `9000` 端口，这在MAC OS下默认的 PHP-FPM 的 9000 端口正好产生了冲突，所以我们需要修改一下xDebug使用的端口（如果你打算修改 PHP-FPM 的端口？我试过了，PHP无法解析了）

找到你的PHP.INI文件，这个默认一般在`/usr/local/etc/php56/`路径下，如果找不到，可以通过`find / -name php.ini`命令来查找该文件的位置。

找到之后，在末尾添加几行

``` ini
[xdebug]
 xdebug.default_enable=1
 xdebug.remote_enable=1
 xdebug.remote_handler=dbgp
 xdebug.remote_host=localhost
 xdebug.remote_port=9001 ;这里要记得不能使用默认的9000端口
 xdebug.remote_autostart=1
 xdebug.remote_log="/var/log/xdebug.log" ;xdebug的日志文件
 xdebug.idekey="vscode-xdebug" ;目前我也不清楚干嘛用的
 zend_extension="/Applications/MAMP/bin/php/php5.4.10/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so"
 ```

并且，由于xDebug和Zend有冲突，所以请把zend的相关参数要注释掉。

典型就是如下几条：

``` ini
[Zend]
;zend_extension_ts = "C:\Program Files\xampp\php\zendOptimizer\lib\ZendExtensionManager.dll"
;zend_extension_manager.optimizer_ts = "C:\Program Files\xampplite\php\zendOptimizer\lib\Optimizer"
;zend_optimizer.enable_loader = 0
;zend_optimizer.optimization_level=15
;zend_optimizer.license_path =
```

如果上面的已经被注释掉了，就OK了。

完成后，进入VSCODE，依次选择左侧的 `Debug按钮`，点击`齿轮`，在下拉菜单中选择 `PHP`

然后，会自动生成一个`launch.json`文件

``` json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9001
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 9001
        }
    ]
}
```

唯一需要注意的，就是把 `port` 也修改为 `9001`（和你在`php.ini` 文件中的 `xdebug.remote_port=9001`一致即可。

## 如何确定xDebug安装成功

用浏览器打开 `http://localhost/phpinfo.php`

然后在结果中使用 `Cmd+F` 搜索 `xDebug` ，如果存在则说明配置成功。

再上个图

![VSC中的DEBUG断点](breakpointinvsc.jpg)

## 参考资料

1. [xDebug百度百科词条](http://baike.baidu.com/view/1823486.htm)
2. [NETBEANS官方出的XMAPP配置XDEBUG说明](https://netbeans.org/kb/docs/php/configure-php-environment-windows_zh_CN.html)
3. [XDEBUG官方一种贴心的配置方法](http://www.blogdaren.com/m/?post=2062)
