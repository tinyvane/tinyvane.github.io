---
title: 如何在CENTOS6上安装GHOST
date: 2016-07-07 15:35:49
categories: CentOS
tags: [centos, ghost, linux]
urlname: how_to_install_ghost_on_centos_6
---

## 文章更新

1. 20160707-初次成文

## 为什么会有这篇文章

因为想多试试不同的博客风格，而且据说GHOST和WP很像，并不是纯静态博客，所以才有了这个帖子。<!-- more -->

## 安装准备

接手一台新的服务器之前，必然是修改root密码，更改ssh端口，ssh免密连接，以及添加Yum源并且update这样的工作，就不多废话了。

## 安装Nginx

### 配置 Nginx yum 源

新建并编辑 Nginx yum 源文件：vim /etc/yum.repos.d/nginx.repo

``` bash
[nginx]
name=nginx repo  
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/  
gpgcheck=0  
enabled=1
```

### 安装 Nginx

``` bash
yum install nginx -y 	#安装 Nginx
```

然后查询下nginx的状态，由于EL6和EL7的区别，所以，如果你在CENTOS6下，是没有systemctl命令的，因为EL6是基于SYSV管理服务，而EL7是基于systemD来管理服务，所以命令上回有所不同。

如果是EL7 

``` bash
systemctl enable nginx 	#设置 Nginx 随服务器自动启动
systemctl start nginx 	#启动 Nginx
systemctl status nginx 	#查看 Nginx 状态，看到如图enabled 和 active 状态，则说明 Nginx 设置成功：
```

如果是EL6

``` bash
sudo service nginx status
sudo service nginx start
sudo service nginx status
```

### 查询nginx状态

在浏览器访问服务器 IP 地址，如果可以看到 Nginx 的 Welcome to Nginx! 欢迎信息，说明 Nginx 配置成功。

### 使用 Nginx 反向代理 Ghost

众所周知，网站基本都以 80 端口作为默认端口（除非你不想让别人访问你的网站），也就是说：访问域名或服务器 IP 地址，实际上是在访问该服务器的 80 端口。而 Ghost 默认运行在 2368 端口上。

为了输入域名或服务器 IP 地址就可以访问到你的 Ghost 博客，需要将访问 80 端口的请求，反向代理到 Ghost 的 2368 端口上：

``` bash
cd /etc/nginx/conf.d #进入 Nginx 配置目录
vim ghost.conf #新建 Ghost 博客的 Nginx 配置文件
```

输入下面的代码

``` bash
server {  
    listen 80;
    # 下面修改为你的域名；如果没有域名，则输入服务器 IP 地址，并且记得末尾的分号
    server_name your_domain_or_ip; 
    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://127.0.0.1:2368;
    }
}
```

修改 default.conf 文件名为 default.conf.bak，也就是使这个 Nginx 的默认配置文件失效，否则会跟上面的 Ghost 配置文件冲突

``` bash
mv default.conf default.conf.bak
```

重启 Nginx

``` bash
systemctl restart nginx 	#如果是EL7
sudo service nginx restart 	#如果是EL6
```

使用 nginx -t 测试 Nginx 配置文件是否语法正确：若输出的结果有 ok、successful 等字样，表示配置正确

-------------------------------
## 添加用于管理博客的用户 www

Linux 系统有一套完整方便的用户权限控制方法。在生产环境部署应用时，使用 root 这个拥有服务器最高权限的用户是有风险的，而应该遵循最小权限原则，也就是说：只需要使用能满足应用正常运行的最低权限的用户即可。我们应该保持这样的良好习惯。

``` bash
useradd -d /var/www www #添加用户 www 并指定其默认用户目录为 /var/www
passwd www 				#为用户 www 设置密码
usermod -aG wheel www 	#把用户 www 添加到 wheel 用户组（方便用户 www 使用 sudo 命令）
groups www 				#查看用户 www 的用户组，此时输出的结果应该是：www : www wheel，表明用户 www 位于 www 和 wheel 两个用户组
```

-------------------------------
## 安装 Node.js

``` bash
curl --silent --location https://rpm.nodesource.com/setup_4.x | bash -
yum install nodejs -y
node -v #显示版本，我这里是4.4.7
```

### 另外一个安装的办法

我并没有尝试，因为还没有系统的学习，就不想添加负担了，先继续把东西装好是第一位的。

从这个步骤开始，所有操作均在用户 www 下完成，即通过su www来切换到www用户下即可。

建议 Linux 和 OS X 用户使用 n 管理 Node.js 版本，Windows 用户使用 Nodist

安装 n 和 Node.js

su www #切换至用户 www
curl -L http://git.io/n-install | N_PREFIX=/var/www bash #安装 n

重启 SSH 窗口，即可使用 n
安装 Node.js v0.10.* 和 Node.js LTS：n 0.10 和 n lts，等待完成
切换 Node.js 版本到 v0.10：输入n，用键盘方向键选择版本，如图：

切换node.js版本

温馨提示：严重建议使用 Node.js v0.10.* 运行 Ghost，其他版本暂时有各种大大小小的问题。

## 安装 Ghost 中文版

将 Ghost 安装在 /var/www/ghost 目录下：

mkdir /var/www 	#创建目录
cd /var/www 	#进入
wget http://dl.ghostchina.com/Ghost-0.7.4-zh.zip 	#下载 Ghost v0.7.4 中文版
unzip Ghost-0.7.4-zh.zip -d ghost   				#解压 Ghost 中文版压缩包到 ghost 文件夹：
cd ghost 											#进入 ghost 文件夹
cp config.example.js config.js  					#从示例配置文件复制并新建 Ghost 配置文件 config.js
vim config.js 				#找到其中的 production 字段，也就是生产环境下的配置项，修改其中的 url 为自己的域名或服务器 IP 地址

我的 config.js 配置文件如下：

``` bash
production: {  
        url: 'your domain or ip',
        mail: {},
        database: {
            client: 'sqlite3',
            connection: {
                filename: path.join(__dirname, '/content/data/ghost.db')
            },
            debug: false
        },

        server: {
            host: '127.0.0.1',
            port: '2368'
        },

        storage: {
            provider: 'local-file-store'
        }
    },
```

``` bash
npm install --production 	#安装 Ghost 依赖
npm start 					#测试 Ghost 是否安装成功
```

然后遇到了错误

``` bash
ERROR: Unsupported version of Node
Ghost needs Node version ~0.10.0 || ~0.12.0 || ~4.2.0 you are using version 4.4.7
```

看来Ghost对node.js的版本确实比较挑剔，只好卸载了4.4.7，来重新安装Ghost的指定版本了。

yum remove nodejs -y

网上找了一下，在EL6下，没有4.2.x版本的node.js，所以只好找0.10.x了

地址在这里，http://nodejs.org/dist/latest-v0.10.x/node-v0.10.46-linux-x64.tar.gz
然后，说一下没有找到rpm安装包，所以只能编译安装了。

为了编译node.js，需要yum install gcc make，或者yum -y groupinstall "Development Tools"

``` bash
yum -y groupinstall "Development Tools" #安装开发编译工具包，或者使用 yum install gcc-c++ make
cd /usr/local/src #一般把下载的源代码放在这个目录下面
wget http://nodejs.org/dist/latest-v0.10.x/node-v0.10.46.tar.gz
tar zxf node-*.tar.gz  
cd node-v*  
./configure #准备进行编译，配置参数，虽然node.js没有这个步骤
make 		#编译
sudo make install 	#安装
ln -s /usr/local/bin/node /usr/bin/node #创建软链接
ln -s /usr/local/bin/npm /usr/bin/npm	#创建软链接
node --version  #或者node -v 检查版本
npm --version  	#或者npm -v 检查版本
```

node.js和npm就安装好了，然后执行

``` bash
npm install --production 	#安装 Ghost 依赖
npm start 					#测试 Ghost 是否安装成功
```

又一个新错误

``` bash
ERROR: Cannot find module '/var/www/ghost/node_modules/sqlite3/lib/binding/node-v11-linux-x64/node_sqlite3.node'
```

从网上看了看这个错误，应该是环境编译有问题，重新来一次

``` bash
rm -rf node_modules && npm cache clean 
npm install --production
```

这次虽然遇到了2个warn，但是最后成功了。

然后

npm start 

浏览器输入购买的域名或服务器 IP 地址，如果能看到 Ghost 博客，说明 Ghost 安装成功
关闭 Ghost：按 Ctrl + C

## PM2 使 Ghost 永久运行

开发模式下的 Ghost 在你退出 SSH 终端窗口后，便会自动关闭。如果要让 Ghost 一直运行，需要使用进程守护软件守护 Ghost 进程，Node.js 社区比较火的两款进程守护软件为 Forever 和 PM2。这里使用 PM2 作为演示。

npm install -g pm2 		#用 npm 全局安装 PM2
cd /var/www/ghost  		#必须进入 Ghost 博客目录：
NODE_ENV=production pm2 start index.js --name "ghost" #在 Ghost 博客目录下，让 Ghost 以 Production 模式（生产模式）运行，指定程序入口文件为 index.js，并且将此进程命名为 ghost
pm2 startup centos  	#使 Ghost 开机自启动
pm2 save   				#保存目前的 PM2 配置

温馨提示：PM2 重启进程的命令是 `pm2 restart 进程名`
恭喜你！现在，浏览器中输入你的域名或服务器 IP 地址，就可以访问你的 Ghost 博客啦！浏览器地址栏增加 /ghost，如 http://loyalsoldier.me/ghost 即可访问 Ghost 博客管理后台。

使用pm2 restart ghost，没问题，然后使用pm2 show ghost，结果显示错误

``` bash
┌───────────────────┬───────────────────────────────────┐
│ status            │ errored                           │
│ name              │ ghost                             │
│ restarts          │ 60                                │
│ uptime            │ 0                                 │
│ script path       │ /var/www/ghost/index.js           │
│ script args       │ N/A                               │
│ error log path    │ /root/.pm2/logs/ghost-error-0.log │
│ out log path      │ /root/.pm2/logs/ghost-out-0.log   │
│ pid path          │ /root/.pm2/pids/ghost-0.pid       │
│ interpreter       │ node                              │
│ interpreter args  │ N/A                               │
│ script id         │ 0                                 │
│ exec cwd          │ /var/www/ghost                    │
│ exec mode         │ fork_mode                         │
│ node.js version   │ 0.10.46                           │
│ watch & reload    │ ✘                                 │
│ unstable restarts │ 0                                 │
│ created at        │ N/A                               │
└───────────────────┴───────────────────────────────────┘
```

使用cat查看/root/.pm2/logs/ghost-error-0.log，发现错误是

``` bash
Error: invalid site url
    at ConfigManager.validate (/var/www/ghost/core/server/config/index.js:349:31)
```

原来是卡在这个函数上面

``` js
// Check that our url is valid
    if (!validator.isURL(config.url, {protocols: ['http', 'https'], require_protocol: true})) {
        errors.logError(new Error('Your site url in config.js is invalid.'), config.url, 'Please make sure this is a valid url before restarting');

        return Promise.reject(new Error('invalid site url'));
    }
```

才知道，原来在config.js里面的url，需要https或者http开头，我直接写了个IP就不行啦，好办，直接改成http://ip

再次pm2 restart ghost && pm2 show ghost，ok了这次。

第一次访问 Ghost 博客管理后台，需要按照流程注册一个账号，这个账号用于登录和管理 Ghost。以后，只需要访问 http://你的域名或服务器 IP 地址/ghost 即可撰写、发表博文！

## 用 Git 为 Ghost 博客进行版本控制

使用版本控制是一个技术人员必备的技能，也是一个好习惯。每次对内容的修改，推送到远程的版本库进行版本控制，有几个好处：

这些修改永远不会丢失（相当于内容备份）；
万一修改出错，可以随时切换回旧版本的内容（相当于修改容错功能）；
记录应用的改版历程……
下面让我们以 Github 为例，看看怎么为 Ghost 博客使用 Git 版本控制服务（国内类似服务有 Git@OsChina）：

注册 Github 账号
浏览器打开 https://github.com/new 新建 Github 仓库（git repo）：如图填写仓库名称和描述，点击Create Repository（创建仓库）：

创建Github仓库

进入 Ghost 博客目录：cd /var/www/ghost
添加 Github 账号到 Git 中：git config --global user.email "you@example.com"（you@example.com 替换为注册 Github 的邮箱账号），然后添加 Github 用户名：git config --global user.name "Your Name"（Your Name 替换为你的 Github 账号昵称）
初始化 Git 仓库：git init
将刚刚新建的 Github 仓库地址添加到 Ghost 目录：git remote add origin https://github.com/你的 Github 用户名/你的仓库名称.git，如图红色方框所示：

添加远程仓库

把 Ghost 博客目录下所有文件改动添加到 Git 暂存区（Git Index）：git add .（这里确实有个英文句号）
提交更改到 Git 本地仓库（Local Repository）：git commit -m "XXXX"（XXX 部分替换为本次提交修改的内容，如“发布博文《在阿里云 CentOS 7 系统上部署 Ghost 博客》”）
把本次更改推送到远程仓库（Upstream Repository）：git push -u origin master
每次对 Ghost 博客目录的文件或内容进行了修改，或发布了新博文，都可以重复步骤 7~9，将修改推送到 Github，称之为版本控制。

## 升级 Ghost 到最新版本

最新版 Ghost 中文版可以在 Ghost 中文网 找到
Ghost 中文版更新方法参照 这个说明 中的“如何升级”部分

## 参考文章

1. [在阿里云 CentOS 7 系统上部署 Ghost 博客](http://www.loyalsoldier.me/deploy-ghost-on-centos-7/)

