---
title: XAMPP在Windows平台的配置流程
date: 2016-09-23 02:00:07
categories: 折腾
tags: [xampp, windows, php, apache, mysql]
urlname: the_note_of_config_xampp_on_windows_10
---

## 文章更新

1. 20160923-初次成文
2. 20161009-补充内容，基本完成。

## 为什么会有这篇文章

因为自己总忘记一些最基本的东西，所以还是写个笔记吧。

## 安装

去官方网站下载XAMPP的安装程序，链接猛戳[这里](https://www.apachefriends.org/zh_cn/download.html)，才发现原来XAMPP不单有WINDOWS版本，还有LINUX和MAC版本。

安装过程没啥说的，个人建议，最好装在硬盘的根目录下的d:\xampp目录下。

## 无法启动相关服务

启动APAPCHE和MYSQL，主要的问题是经常会遇到80端口被IIS占用，从而导致APACHE无法顺利启动。

表现就是点击XAMPP Control Panel程序的APACHE服务的start按钮的时候，下方状态栏会显示

``` accesslog
busy apache started [port 80]
```

### 解决方法1：查找占用的程序 

1. `Win+r`,输入`cmd`，在控制台输入`netstat -ano`，回车 
2. 查看结果中包含`xx.xx.xx.xx:80`的那一行的`pid`，为几个数字，把这几个数字记下来； 
3. 启动`任务管理器`——`进程`，在工具栏——`选择列`前面的框打上勾； 
4. 然后查看与刚才那个`pid`对应的是哪个程序，很容易就会找到，就是它占用了80端口； 
5. 直接将其停止即可。
 
### 解决方法2：更换端口 

可以将apache的默认80端口修改为`8081`(随便其它的只要不被占用就可以了) 

到xampp的安装目录下，点击进入apache\conf下，可以看到“httpd.conf”文件，用文本编辑器打开，将所有的80修改为8081，

``` conf
Listen 8081
ServerName localhost:8081 
```

然后在XAMPP Control Panel中重新启动apache。

## 多域名配置

### hosts文件修改

在自己机器上开发，肯定不是一个PHP网站的，所以要建立多个网站的别名访问

先到WINDOWS的安装盘，依次进入`WINDOWS`-`SYSTEM32`-`DRIVERS`-`ETC`目录，找到`HOSTS`文件

基本上就是这样一个路径`C:\Windows\System32\drivers\etc`。

添加几行

``` ini
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
    DocumentRoot "E:/xampp/htdocs/peipei.com"
    ServerName peipei.com
    ErrorLog "logs/peipei.com-error.log"
    CustomLog "logs/peipei.com-access.log" common
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "E:/xampp/htdocs/xiaowei.com"
    ServerName xiaowei.com
    ErrorLog "logs/xiaowei.com-error.log"
    CustomLog "logs/xiaowei.com-access.log" common
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "E:/xampp/htdocs/meimei.com"
    ServerName meimei.com
    ErrorLog "logs/meimei.com-error.log"
    CustomLog "logs/meimei.com-access.log" common
</VirtualHost>
```

写完才发现，其实有很大一部分内容，和我的另外一个帖子[《在Windows下进行PHP开发》](http://www.wuliaole.com/post/php_development_on_windows)内容是重复的，怎么办呢？就这样招吧，谁让重复就是力量呢。


