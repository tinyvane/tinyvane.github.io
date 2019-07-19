---
title: MAC OS系统使用Homebrew配置NGINX PHP和MYSQL
date: 2016-07-01 09:55:44
categories: 折腾
tags: [mac, php, homebrew, nginx, mysql, php-fpm]
urlname: config_nginx_php_and_mysql_on_mac_using_homebrew
---

## 文章更新

1. 20160701-初次成文
2. 20160704-完整配置完成
3. 20160708-整理小错误，理顺一些不通顺的地方
4. 20160815-系统升级到了CAPITAN，一些地方重新修改
5. 20160928-补充PHPMYADMIN的使用注意事项
6. 20161006-补充xDebug和VSCode的配置内容
7. 20170218-修改了一些小错误
8. 20170728-调整Homebrew内容到其他帖子

## 为什么会有这篇文章

MAC OS系统下，全部使用Homebrew来配置NGINX, PHP以及MYSQL的文章并不多，所以自己总结了整个安装过程。本文针对EI CAPITAN或者更高的MAC OS版本。<!-- more -->

## 确认Homebrew就绪

Homebrew的安装可以参见另外一个[帖子](http://www.wuliaole.com/post/the_tutorial_101_of_homebrew_on_mac)

## 删除旧版PHP

在安装新版PHP之前，首先要删掉系统自带的旧版PHP程序，我的系统是 OSX EI CAPITAN，PHP版本是5.5.38，根据网上的一篇[文章](http://www.cnblogs.com/meixinghao/articles/4681041.html)，写了个脚本

为了方便，最好先切换到root账户下。

``` bash
cd /private/etc/               
rm -rf php-fpm.conf.default php.ini php.ini.default
cd /usr/bin/               
rm -rf php php-config phpdoc phpize
cd /usr/include 
rm -rf php
cd /usr/lib
rm -rf php
cd /usr/sbin               
rm -rf php-fpm
cd /usr/share              
rm -rf php
cd /usr/share/man/man1         
rm -rf php-config.1 php.1 phpize.1
cd /usr/share/man/man8         
rm -rf php-fpm.8
```

上述操作，在EI CAPITAN之前的MAC版本下执行不会遇到问题，而在EI CAPITAN下会遇到`Operation not permitted`的权限错误。这是因为自从EI CAPITAN开始，MAC系统默认开启了`System Integrity Protection`（SIP，系统完整性保护），从而导致用户无法在系统目录下执行操作。

若要关闭SIP，需重启系统，进入 `recover模式`（重启之后按住 `Command+r`），在 `工具` 中找到 `terminal` 执行 `csrutil disable` 命令，然后重启，SIP保护就被关闭了。 

## 安装PHP和PHP-FPM

因为Homebrew的库中的PHP默认没有带PHP-FPM，因此需要首先tap（或者说登记）一个特殊的PHP库

``` bash
brew tap homebrew/dupes
brew tap homebrew/homebrew-php
```

更多类库，可以到GITHUB上查看更多，链接戳[这里](https://github.com/Homebrew/)

现在，就可以使用下面命令来安装PHP-FPM了，并且为了保证同时编译MySQL，以及不配置默认的apache，需要使用下面的参数：

``` bash
brew install php56 --without-apache --with-fpm --with-mysql 
```

上面的意思就是安装PHP 5.6，不装APACHE，附带FPM以及MYSQL。

安装时间视网络和机器速度而定，我装了大约半个小时。

### 错误：Cannot find libz

``` accesslog
checking if the location of ZLIB install directory is defined... no
configure: error: Cannot find libz

READ THIS: https://git.io/brew-troubleshooting
If reporting this issue please do so at (not Homebrew/brew):
  https://github.com/Homebrew/homebrew-php/issues
```

说明安装程序无法找到libz库，在新的OS系统下，这通常是因为xcode的更新到7或者8之后，缺少命令行工具造成的

运行

``` bash
xcode-select --install 
brew upgrade
```

继续

### 错误：insufficient permission for adding an object to repository database .git/objects

``` accesslog
error: insufficient permission for adding an object to repository database .git/objects
fatal: failed to write object
fatal: unpack-objects failed
Error: Fetching /usr/local/Library/Taps/homebrew/homebrew-core failed!
```

这个错误很典型，在Homebrew的使用过程中很常见，稍微留意就可以知道和上面遇到的错误一样，都是Homebrew缺少某目录的操作权限，可以参考上面的错误处理方式。

输入命令

``` bash
sudo chown -R $(whoami) /usr/local/Library/Taps/homebrew/
```

### 错误：The brew link step did not complete successfully

``` accesslog
==> make install
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink.
/usr/local/Library/LinkedKegs is not writable.

You can try again using:
  brew link php56
==> Caveats
The php.ini file can be found in:
    /usr/local/etc/php/5.6/php.ini
```

按照系统提示，执行 `brew link php56`，让Homebrew重新链接相应的文件。

``` accesslog
Linking /usr/local/Cellar/php56/5.6.23...
Error: Could not symlink .
/usr/local/Library/LinkedKegs is not writable.
```

还是权限问题，继续 `sudo chown -R $(whoami) /usr/local/Library/LinkedKegs`，再次执行 `brew link php56`，搞定，继续。

## PHP-FPM的启动和使用

好了，终于顺利装好了php56，静下心来看看输出的信息。

> 需要注意的是，PHP 5.6的安装，连带一起安装了PHP-FPM，这个东西怎么说呢，我自己也不是很理解，可以说是一种替代了FASTCGI的控制器，但是什么是FASTCGI，或者什么是CGI，我也说不清楚，反正就是多了个PHP-FPM。

``` accesslog
✩✩✩✩ Extensions ✩✩✩✩

If you are having issues with custom extension compiling, ensure that
you are using the brew version, by placing /usr/local/bin before /usr/sbin in your PATH:

      PATH="/usr/local/bin:$PATH"

PHP56 Extensions will always be compiled against this PHP. Please install them
using --without-homebrew-php to enable compiling against system PHP.

✩✩✩✩ PHP CLI ✩✩✩✩

If you wish to swap the PHP you use on the command line, you should add the following to ~/.bashrc,
~/.zshrc, ~/.profile or your shell's equivalent configuration file:

      export PATH="$(brew --prefix homebrew/php/php56)/bin:$PATH"

✩✩✩✩ FPM ✩✩✩✩

To launch php-fpm on startup:
    mkdir -p ~/Library/LaunchAgents
    cp /usr/local/opt/php56/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/
    launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist

The control script is located at /usr/local/opt/php56/sbin/php56-fpm

OS X 10.8 and newer come with php-fpm pre-installed, to ensure you are using the brew version you need to make sure /usr/local/sbin is before /usr/sbin in your PATH:

  PATH="/usr/local/sbin:$PATH"

You may also need to edit the plist to use the correct "UserName".

Please note that the plist was called 'homebrew-php.josegonzalez.php56.plist' in old versions of this formula.

To have launchd start homebrew/php/php56 now and restart at login:
  brew services start homebrew/php/php56
==> Summary
🍺  /usr/local/Cellar/php56/5.6.23: 330 files, 38M, built in 5 minutes 30 seconds
```

在最后更新这个帖子的时候，PHP-FPM又更新到了5.6.24。

大概看了下上面的输出信息，有几个需要注意的地方：

1. 由于MAC OS 10.8或者更新的系统，预装了PHP-FPM，所以如果想使用brew安装的PHP-FPM，需要确保用户目录`/usr/local/bin`位于系统路径之前(至少要前于 `/usr/sbin`)，比如执行 `PATH="/usr/local/bin:$PATH"`
2. 如果你希望在BASH或者ZSH的控制台环境下使用PHP命令行工具，需要在`.bashrc`或者`.zshrc`文件的最后添加一行 `export PATH="$(brew --prefix homebrew/php/php56)/bin:$PATH"`
3. 如果需要PHP-FPM随系统自启，则依次执行3条命令：
    1. `mkdir -p ~/Library/LaunchAgents` #参数p是根据需要创建中间目录。
    2. `ln -s /usr/local/opt/php56/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/`。
    3. `launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist`。关于参数w的用法，我使用`man launchctl`看了一下，得到了这段解释

        > Overrides the Disabled key and sets it to false or true for the load and unload subcommands respectively. In previous versions, this option would modify the configuration file. Now the state of the Disabled key is stored elsewhere ondisk in a location that may not be directly manipulated by any process other than launchd.

        意思是说这个参数将改写`disable key`参数，在加载plist之后将其改写为`false`，在卸载plist之后将其改写为`true`。在更早的版本中，使用这个参数将改写服务的配置文件。现在，`disabled key`参数将保存在磁盘上的某个位置，除了`launchd`程序外，其他程序并不可以直接对其进行操作。

        说点其他的，在配置php on mac的整个过程中，基本上有3个plist文件需要launchctl进行加载或者卸载。分别是`homebrew.mxcl.nginx.plist`、`homebrew.mxcl.php56.plist`和`homebrew.mxcl.mysql.plist`，这三个文件存放的位置位置并不尽相同，nginx.plist存放在`/Library/LaunchDaemons/`目录下，而php56.plist和mysql.plist则通过软链接到了`~/Library/LaunchAgents`目录下，并且，nginx.plist文件修改owner为root，而后两者则没有，但是却带了-w参数。关于LaunchDaemon和LaunchAgents的区别，网上有两个篇文章讲解得不错，分别是：
            
            * [了解LaunchDaemons](http://afoo.me/posts/2014-12-12-understanding-launch-daemons-of-macosx.html)
            * [LaunchDaemons vs LaunchAgents](http://www.grivet-tools.com/blog/2014/launchdaemons-vs-launchagents/)

4. 控制命令脚本位于 `/usr/local/opt/php56/sbin/` 下，名称为`php56-fpm`

关于第二点，多说几句。如果你使用bash shell，添加的命令为

``` bash
echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.bash_profile && . ~/.bash_profile
```

如果你的ZSH shell，命令是

``` bash
echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.zshrc && . ~/.zshrc
```

如果你也不确定自己用的哪种shell，使用 `echo $SHELL` 命令查看。

### 错误分析

#### 找不到PHP-FPM的配置文件

如果运行PHP-FPM提示找不到配置文件，则可以通过 `find / | grep php-fpm.conf.default`，看看默认文件在哪里，然后CP一个过去。

``` bash
cp /usr/local/etc/php/5.6/php-fpm.conf.default /usr/local/etc/php/5.6/php-fpm.conf
```

或者

``` bash
php-fpm --fpm-config /usr/local/etc/php/5.6/php-fpm.conf #指定配置文件的位置
```

#### 日志打开错误

然后，再次手动运行`php-fpm`，又遇到新的错误。

``` accesslog
[04-Jul-2016 23:22:25] ERROR: failed to open error_log (/usr/local/var/log/php-fpm.log): No such file or directory (2)
[04-Jul-2016 23:22:25] ERROR: failed to post process the configuration
[04-Jul-2016 23:22:25] ERROR: FPM initialization failed
```

错误信息显示：不能正确的打开"日志"文件，原因是php-fpm在 `/usr/local/var/log/` 找不到名为`php-fpm.log`的日志文件。

打开配置文件，

``` bash
vim /usr/local/etc/php/5.6/php-fpm.conf
```

修改文件中 `error_log` 项，默认前缀是 `/usr/var`，但并没有这个路径，要修改为 `/usr/local/var`

``` conf
error_log = /usr/local/var/log/php-fpm.log
pid = /usr/local/var/run/php-fpm.pid
```

或者不修改配置文件中配置项的路径，在php-fpm的运行参数中添加参数prefix，指定放置运行时文件的相对路径前缀

``` bash
php-fpm --fpm-config /usr/local/etc/php-fpm.conf --prefix /usr/local/var
```

到此，php-fpm守护进程已经基本可以正确的启动了。

再次运行 `php-fpm -v`，已经可以成功得到版本号了，5.6.26，GAME OVER.

如果还不放心，可以通过查看内存中端口号为9000的进程，如果看到了PHP-FPM，则说明确实运行成功了。

``` bash
lsof -Pni4 | grep LISTEN | grep php
```

或者简单的

``` bash
lsof -i :9000
```

结果

``` accesslog
php-fpm   35370           root    7u  IPv4 0x7a8545f6d5d08f1d      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   35371           _www    0u  IPv4 0x7a8545f6d5d08f1d      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   35372           _www    0u  IPv4 0x7a8545f6d5d08f1d      0t0  TCP 127.0.0.1:9000 (LISTEN)
```

> 这里提前说一下这个端口9000的问题，我在调试PHP代码的时候使用了xDebug，但是如果按照默认设置的话，经常无法正常调试，后来终于发现是因为xDebug也使用了9000端口，我个人是把XDEBUG的端口修改为了9001才正常的。话说网上的说法是XDEBUG先定义的9000端口，然后PHP-FPM蛮横的也用了9000....

## 安装MySQL

``` bash
brew install mysql
```

设置MySQL随你账户的启动和停止。

``` bash
ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
```

想手动启动的话，运行：

``` bash
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

使用`mysql -V`或者`--version`查看MySQL版本(注意不要错写成`mysql -v`因为小写v是--Verbose的缩写，而不是Version的缩写)

### 增强Mysql安全性

为了增强Mysql服务器的安全性，我们可以通过运行 `secure_mysql_installation` 这个程序，来修改root密码，去掉匿名账户，并且关闭远程root登录权限。

运行后，会进入下面的设置过程（2016年8月15日更新）：

``` accesslog
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No: #设置一个 VALIDATE PASSWORD 的插件帮你完成任务，选Y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: #我这里选的3，最强的

New password: #输入密码

Re-enter new password: #再次输入密码

Estimated strength of the password: 100 #显示你设置的密码强度
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : #认可的话，选择Y继续

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : #是否删掉匿名账户，Y
Success.

Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : #禁止ROOT账户远程登录，Y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : #删除测试数据库以及相应的访问权限，Y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : #重新载入权限以测试上述设置是否成功，Y
Success.

All done!
```

### 测试Mysql连接

命令 `mysql -uroot -p`，输入刚才设置的密码

``` accesslog
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 5.7.14 Homebrew

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> quit
Bye
```

#### 如何修改mysql的root账号密码

使用上面的命令(mysql -uroot -p)进入mysql命令行界面，然后输入

``` mysql
set password for 'root'@'localhost'=password('passwd');
```

显示结果

``` accesslog
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

不用担心显示`0 rows affected`, 密码是改了的，只是需要继续刷新权限。

``` mysql
flush privileges;
```

显示结果

``` accesslog
Query OK, 0 rows affected (0.00 sec)
```

显示的是`0 rows affected`，别担心，quit推出mysql命令行，然后再次继续的时候，你会发现mysql密码已经修改成了新的密码，旧的密码已经失效。

好了，简单的说，更新mysql用户的密码，有3种方式

1. 可以使用 `set password for 'user_name'@'host_name'=password('new_pwd')` 方式来修改密码
2. 可以使用 `update` 系统表方式，`update user set password=password('passwd') where user='user_name'`
  注： 对于user表password类，如果不用password函数的话，导致更新后无法登陆。但如果将账户更新为空密码，可以使用加密函数，也可以不使用，2者等同。
3. 也可以在用户创建后直接使用grant方式来更新用户密码。

> 注意：对应root密码丢失或需要重置root密码的情形，需要使用系统选项`--skip-grant-tables`启动服务器后进行重置。

具体可以参考这篇文章：《[MySQL 修改用户密码及重置root密码](http://blog.csdn.net/leshami/article/details/39805839)》

## 安装phpMyAdmin

### 安装autoconf

phpMyadmin的安装需要autoconf

``` bash
brew install autoconf
```

结果

``` accesslog
==> Downloading https://homebrew.bintray.com/bottles/autoconf-2.69.el_capitan.bottle.4.tar.gz
######################################################################## 100.0%
==> Pouring autoconf-2.69.el_capitan.bottle.4.tar.gz
==> Caveats
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/autoconf
==> Summary
🍺  /usr/local/Cellar/autoconf/2.69: 70 files, 3.0M
```

然后设置`$PHP_AUTOCONF`环境变量

如果使用默认的bash shell，使用命令：

``` bash
echo 'PHP_AUTOCONF="'$(which autoconf)'"' >> ~/.bash_profile && . ~/.bash_profile
```

如果是ZSH shell，使用命令：

``` bash
echo 'PHP_AUTOCONF="'$(which autoconf)'"' >> ~/.zshrc && . ~/.zshrc
```

准备工作完成，开始安装phpMyAdmin

``` bash
brew install phpmyadmin
```

看看结果

``` accesslog
Webserver configuration example (add this at the end of
your /etc/apache2/httpd.conf for instance) :
  Alias /phpmyadmin /usr/local/share/phpmyadmin
  <Directory /usr/local/share/phpmyadmin/>
    Options Indexes FollowSymLinks MultiViews
    AllowOverride All
    <IfModule mod_authz_core.c>
      Require all granted
    </IfModule>
    <IfModule !mod_authz_core.c>
      Order allow,deny
      Allow from all
    </IfModule>
  </Directory>
Then, open http://localhost/phpmyadmin

More documentation : file:///usr/local/Cellar/phpmyadmin/4.6.2/share/phpmyadmin/doc/

Configuration has been copied to /usr/local/etc/phpmyadmin.config.inc.php
Don't forget to:
  - change your secret blowfish
  - uncomment the configuration lines (pma, pmapass ...)

==> Summary
🍺  /usr/local/Cellar/phpmyadmin/4.6.3: 2,257 files, 63.2M, built in 5 minutes 29 seconds
```

结果里面的介绍的使用Apache时候的配置方法，使用Nginx呢？直接在你的ngnix需要的地方，link出来一个链接即可

``` bash
ln -s /usr/local/share/phpmyadmin ./phpmyadmin
```

注意上面需要进入的你的网站的根目录，如果是/var/www，你也可以写完整的路径 `ln -s /usr/local/share/phpmyadmin /var/www/phpmyadmin`

## 安装 Nginx

命令

``` bash
brew install nginx
```

看结果

``` accesslog
The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To have launchd start nginx now and restart at login:
  brew services start nginx
Or, if you don t want/need a background service you can just run:
  nginx
```

就是告诉你nginx默认使用了8080端口，其定义在 `/usr/local/etc/nginx/nginx.conf` 文件中，这个定义因为端口号大于1024，所以不需要sudo启动，如果需要改为80端口，则需要修改文件Owner为root，这个地方需要注意。后面继续说这个问题。

### Nginx的启动和停止

如果需要背景运行Nginx，输入命令

``` bash
brew services start nginx
brew services stop nginx
```

或者直接输入 `nginx` 命令，前台运行，输入`Control+C`停止运行。

运行后，通过测试网页地址 `http://127.0.0.1:8080` 来看看是否可以看到默认的Nignx页面。

或者通过运行命令`curl -IL http://127.0.0.1:8080` 来看看能否读取到页面信息，也可以间接验证Nginx是否正常。

### 修改端口为80

从上面的结果可以看到 Nginx 默认安装在了8080端口，因为一般调试网页默认都是80端口，因此如果需要Nginx使用80端口，需要让Nginx的启动文件加载在root权限下面，也就是说需要将其启动文件从普通用户更改到root用户下(这是因为只有root用户，才可以使用小于1024的端口）。

修改 `/usr/local/etc/nginx/nginx.conf` 配置文件

将 `listen: 8080` 修改为 `listen: 80` 即可。

先停止nginx，然后使用启动(命令`sudo brew services start nginx`测试一下，因为nginx这时候已经成为root用户的执行文件了，启动和停止都需要 `sudo` 的前缀。

### Nginx自动启动

``` bash
sudo ln -s /usr/local/opt/nginx/homebrew.mxcl.nginx.plist /Library/LaunchDaemons/
sudo chown root:wheel /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

如果不想Nginx自动启动，使用下面的命令

``` bash
sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

### 更多nginx.conf设置

删除默认的 `nginx.conf` 文件，并且下载我在Github上提供的配置文件。（提醒一下，默认配置文件路径是 `/usr/local/etc/nginx/nginx.conf.default`）

``` bash
rm /usr/local/etc/nginx/nginx.conf
curl https://gist.githubusercontent.com/frdmn/7853158/raw/nginx.conf -o /usr/local/etc/nginx/nginx.conf
```

新的配置文件力求轻量化，只涉及了worker设置，日志格式/路径等设置，`nginx.conf.default` 里的其他没用的设置，以及那些被注释掉的废话一律没有。

配置文件涉及了几个目录和文件

``` conf
error_log  /usr/local/etc/nginx/logs/error.log
access_log  /usr/local/etc/nginx/logs/access.log
include /usr/local/etc/nginx/sites-enabled/*
```

手动建立

``` bash
mkdir -p /usr/local/etc/nginx/logs #访问日志存放目录
mkdir -p /usr/local/etc/nginx/sites-available 
mkdir -p /usr/local/etc/nginx/sites-enabled
mkdir -p /usr/local/etc/nginx/conf.d #存放 扩展配置文件 以及 用户配置文件
mkdir -p /usr/local/etc/nginx/ssl #SSL证书以及私钥目录
sudo mkdir -p /var/www
sudo chown :staff /var/www
sudo chmod 775 /var/www
```

这里说一下`site-available`和`sites-enable`的区别

1. `sites-available` - 配置 虚拟主机（存放配置文件，代表多个虚拟主机的配置）
2. `sites-enabled` - 存放 软链接，指向 `sites-available` 中的配置文件，实际上，nginx 是加载 `sites-enabled` 下的配置，作为虚拟主机）

这里稍微多说一下如何配置虚拟多网站访问，首先你要修改`/etc`下的hosts文件，这个和windows系统下的hosts文件基本原理一样。

``` ini
127.0.0.1	peipei.com
127.0.0.1	xiaowei.com
127.0.0.1	meimei.com
```

然后，修改 `sites-available` 目录的 `default` 文件，添加像下面这样的配置。

``` conf
server {
    listen       80;
    server_name  peipei.com www.peipei.com;
    root       '/Users/wangyi/BitTorrent Sync/Sync Gits/php/peipei.com';

    access_log  /usr/local/etc/nginx/logs/default.access.log  main;

    location / {
        include   /usr/local/etc/nginx/conf.d/php-fpm;
    }

    location = /info {
        allow   127.0.0.1;
        deny    all;
        rewrite (.*) /.info.php;
    }

    error_page  404     /404.html;
    error_page  403     /403.html;
}

server {
    listen       80;
    server_name  xiaowei.com www.xiaowei.com;
    root       '/Users/wangyi/BitTorrent Sync/Sync Gits/php/xiaowei.com';

    access_log  /usr/local/etc/nginx/logs/default.access.log  main;

    location / {
        include   /usr/local/etc/nginx/conf.d/php-fpm;
    }

    location = /info {
        allow   127.0.0.1;
        deny    all;
        rewrite (.*) /.info.php;
    }

    error_page  404     /404.html;
    error_page  403     /403.html;
}

server {
    listen       80;
    server_name  localhost;
    root       '/var/www';

    access_log  /usr/local/etc/nginx/logs/default.access.log  main;

    location / {
        include   /usr/local/etc/nginx/conf.d/php-fpm;
    }

    location = /info {
        allow   127.0.0.1;
        deny    all;
        rewrite (.*) /.info.php;
    }

    error_page  404     /404.html;
    error_page  403     /403.html;
}
```

## 安装xDebug

命令

``` bash
brew install <php-version>-xdebug
``` 

比如，上面咱们安装的是php 5.6，那么这里的命令就可以写成

``` bash
brew install php56-xdebug
```

结果

``` accesslog
==> Installing homebrew/php/php56-xdebug
==> Downloading https://homebrew.bintray.com/bottles-php/php56-xdebug-2.4.1.el_c
######################################################################## 100.0%
==> Pouring php56-xdebug-2.4.1.el_capitan.bottle.tar.gz
==> Caveats
To finish installing xdebug for PHP 5.6:
  * /usr/local/etc/php/5.6/conf.d/ext-xdebug.ini was created,
    do not forget to remove it upon extension removal.
  * Validate installation via one of the following methods:
  *
  * Using PHP from a webserver:
  * - Restart your webserver.
  * - Write a PHP page that calls "phpinfo();"
  * - Load it in a browser and look for the info on the xdebug module.
  * - If you see it, you have been successful!
  *
  * Using PHP from the command line:
  * - Run `php -i "(command-line 'phpinfo()')"`
  * - Look for the info on the xdebug module.
  * - If you see it, you have been successful!
==> Summary
🍺  /usr/local/Cellar/php56-xdebug/2.4.1: 2 files, 188.8K
```

没啥说的，主要就是通过两种方式验证xDebug是否安装成功，一是可以通过查看`phpinfo()`函数的输出，另外一个则是看`php -i`，看结果中是否有xDebug的项目即可，有了就OK了。

## 网站配置

``` bash
curl https://gist.githubusercontent.com/frdmn/7853158/raw/php-fpm -o /usr/local/etc/nginx/conf.d/php-fpm #PHP-FPM配置
curl https://gist.githubusercontent.com/frdmn/7853158/raw/sites-available_default -o /usr/local/etc/nginx/sites-available/default #虚拟多域名
curl https://gist.githubusercontent.com/frdmn/7853158/raw/sites-available_default-ssl -o /usr/local/etc/nginx/sites-available/default-ssl #虚拟多域名
curl https://gist.githubusercontent.com/frdmn/7853158/raw/sites-available_phpmyadmin -o /usr/local/etc/nginx/sites-available/phpmyadmin #虚拟多域名
```

使用`git clone`命令clone我的虚拟多域名的网站实例（包含403、404以及一个`phpinfo()`信息页面）：

``` bash
git clone http://git.frd.mn/frdmn/nginx-virtual-host.git /var/www
rm -rf /var/www/.git #删掉.git目录
```

## SSL网站配置

使用下列命令，建立一个4096位的RSA钥匙对，以及自签名的证书：

``` bash
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -subj "/C=US/ST=State/L=Town/O=Office/CN=localhost" -keyout /usr/local/etc/nginx/ssl/localhost.key -out /usr/local/etc/nginx/ssl/localhost.crt
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -subj "/C=US/ST=State/L=Town/O=Office/CN=phpmyadmin" -keyout /usr/local/etc/nginx/ssl/phpmyadmin.key -out /usr/local/etc/nginx/ssl/phpmyadmin.crt
```

## 设置虚拟多域名（virtual hosts）

在sites-enabled目录中设置软连接，链接到sites-available中的对应目录

``` bash
ln -sfv /usr/local/etc/nginx/sites-available/default /usr/local/etc/nginx/sites-enabled/default
ln -sfv /usr/local/etc/nginx/sites-available/default-ssl /usr/local/etc/nginx/sites-enabled/default-ssl
ln -sfv /usr/local/etc/nginx/sites-available/phpmyadmin /usr/local/etc/nginx/sites-enabled/phpmyadmin
```

再次设置Nginx自动启动:

``` bash
sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

## 完毕

恭喜你，所有设置已经完毕，尽情享受你的环境吧。

下面是一些虚拟路径以及作用：

+ http://localhost	#"Nginx works" 页面
+ http://localhost/info	#phpinfo() 页面
+ http://localhost/nope	#"Not Found" 页面
+ https://localhost:443	#"Nginx works" 页面 (SSL)
+ https://localhost:443/info	#phpinfo() 页面(SSL)
+ https://localhost:443/nope	#"Not Found" 页面 (SSL)
+ https://localhost:306	#phpMyAdmin (SSL)

## 使用别名控制服务

为了方便的启动PHP相关服务，可以设置下列别名：

``` ini
alias nginx.start='sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist'
alias nginx.stop='sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist'
alias nginx.restart='nginx.stop && nginx.start'
alias php-fpm.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist"
alias php-fpm.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist"
alias php-fpm.restart='php-fpm.stop && php-fpm.start'
alias mysql.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
alias mysql.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
alias mysql.restart='mysql.stop && mysql.start'
alias nginx.logs.error='tail -250f /usr/local/etc/nginx/logs/error.log'
alias nginx.logs.access='tail -250f /usr/local/etc/nginx/logs/access.log'
alias nginx.logs.default.access='tail -250f /usr/local/etc/nginx/logs/default.access.log'
alias nginx.logs.default-ssl.access='tail -250f /usr/local/etc/nginx/logs/default-ssl.access.log'
alias nginx.logs.phpmyadmin.error='tail -250f /usr/local/etc/nginx/logs/phpmyadmin.error.log'
alias nginx.logs.phpmyadmin.access='tail -250f /usr/local/etc/nginx/logs/phpmyadmin.access.log'
```

现在你可以使用短小的别名来控制服务的启动和停止了，而不用输入长长的 `launchctl` 命令:

### Nginx相关的控制命令

你可以使用下面的命令来启动、停止、重启 Nginx:

``` bash
nginx.start
nginx.stop
nginx.restart
```

快速检查错误和查看日志，使用下面的命令：

``` bash
nginx.logs.access
nginx.logs.default.access
nginx.logs.phpmyadmin.access
nginx.logs.default-ssl.access
nginx.logs.error
nginx.logs.phpmyadmin.error
```

检查配置: `sudo nginx -t`

显示结果

``` accesslog
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
```

### PHP-FPM相关的控制命令

你可以使用下面的命令来启动、停止、重启 PHP-FPM:

``` bash
php-fpm.start
php-fpm.stop
php-fpm.restart
```

检查PHP-FPM配置:`php-fpm -t`

### MySQL相关的控制命令

你可以使用下面的命令来启动、停止、重启 MySQL 服务:

``` bash
mysql.start
mysql.stop
mysql.restart
```

## 常见错误

### 错误 Nginx: '[emerg] mkdir() "/usr/local/var/run/nginx/client_body_temp"'

可能的解决办法：升级到至少Yosemite以上的MAC OS版本，然后重新使用Homebrew强制安装nginx

``` bash
brew reinstall --force nginx
```

### 错误PHP-FPM: 'lsof -Pni4 | grep LISTEN | grep php' doesn't return anything

可能的解决办法：确保你的系统变量路径设置正确，使用命令：

``` bash
echo $PATH | grep php56
```

来检查php56是否在系统路径中，如果没有任何返回结果，你需要调整你的 .zshrc/.bash_profile. 文件设置，确保在这个文件末尾，有这样一行：

``` bash
export PATH="$(brew --prefix homebrew/php/php56)/bin:$PATH"
```

### 错误 git: 'Could not resolve host: git.frd.mn'

可能的解决办法：这可能是我的服务器的问题，请与我联系。

### 错误 curl: 'Failed to connect to localhost port 80: Connection refused'

可能的解决办法：这是一个涉及 IPv6 的问题，问题出在你的Mac电脑的 /etc/hosts 文件。如果想修复这个错误，在hosts文件中，找到 "fe80::1%lo0 localhost" 这一行，并且把他注释掉，或者使用下面的命令一键搞定：

``` bash
sudo sed -i "" 's/^fe80\:\:/\#fe80\:\:/g' /etc/hosts
```

### 错误 brew: 'configure: error: Can not find OpenSSL's '

可能的解决办法：确认Xcode已经安装，并且保证Xcode CLI工具安装并且保持最新版本。

### 错误 访问本地网站出现 '502 bad gateway'的提示。

先检查php-fpm是否顺利执行了。

### 错误：Mavericks: Compilation error while building PHP / missing zlib

可能的解决办法：恢复 `/usr/include` 目录，输入下面的命令：

``` bash
sudo ln -s /Applications/Xcode5-DP.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include /usr/include
```

## 参考资料

1. [INSTALL NGINX, PHP AND MYSQL ON OS X](http://blog.frd.mn/install-nginx-php-fpm-mysql-and-phpmyadmin-on-os-x-mavericks-using-homebrew/)
2. [MySQL 修改用户密码及重置root密码](http://blog.csdn.net/leshami/article/details/39805839)
3. [how to install Xdebug on MAC OS X](https://xdebug.org/docs/install)
4. [nginx、php-fpm、mysql用户权限解析](http://www.ilanni.com/?p=7438)
5. [了解LaunchDaemons](http://afoo.me/posts/2014-12-12-understanding-launch-daemons-of-macosx.html)
6. [LaunchDaemons vs LaunchAgents](http://www.grivet-tools.com/blog/2014/launchdaemons-vs-launchagents/)