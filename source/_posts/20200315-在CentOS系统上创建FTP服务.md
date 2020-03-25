---
title: 在CentOS系统上创建FTP服务
categories: 折腾
tags: [FTP, CentOS]
date: 2020-03-15 14:45:55
urlname: How_to_Create_FTP_service_on_CentOS
---

# 为什么会有这篇文章

最近搭建了DST的独立服务器，游戏的启动和关闭都需要用steam用户，所以做了个VSFTP的服务，用来上传下载一些配置文件。

# 前置条件

1. CentOS系统，Ubuntu也可以，命令稍微不同。
2. 

# 1. 安装VSFTP

```
rpm -qa | grep vsftpd #检查是否已经安装了vsftpd
yum -y install vsftpd #没有装，就新装
systemctl start vsftp #开启vsftpd服务
systemctl enable vsftpd #设置开机启动vsftpd服务 
```

# 2. 创建防火墙规则（允许防火墙放行vsftpd监听端口）

```
firewall-cmd --zone=public --permanent --add-port=21/tcp
firewall-cmd --zone=public --permanent --add-service=ftp
firewall-cmd –reload
```

# 3. 配置vsftp

配置开启虚拟账户（只能登陆ftp,不能登陆系统）以及设置访问目录

vim   /etc/vsftpd/vsftpd.conf

## 3.1 关闭匿名用户、开启本地用户

```
# 禁用匿名用户
anonymous_enable=NO

# 启动本地用户
local_enable=YES
```

## 3.2 开启登录用户上传文件权限

```
write_enable=YES
```

## 3.3 配置登录用户只能访问自己的家目录

```
chroot_local_user=YES

allow_writeable_chroot=YES
```

说明：为了测试目的，我们可以将配置allow_writeable_chroot=YES 将打开，但是管理员采用user_sub_token配置项将更加安全，详情参考：[vsftpd文档](http://vsftpd.beasts.org/vsftpd_conf.html)

## 3.4 配置管理登录用户列表

```
# 开启可登录用户列表管理
userlist_enable=YES
# 用户列表位置
userlist_file=/etc/vsftpd/user_list
# 不拒绝用户列表
userlist_deny=NO
```

 说明：

   a. 我们可以编辑用户列表文件`/etc/vsftpd/user_list`，添加或者删除用户（每一个用户占用一行）。 

   b. `userlist_deny`配置项如果为yes，那么user_list文件中的用户将被锁定，不用登录。

```
anonymous_enable=NO //设定不允许匿名访问
local_enable=YES //设定本地用户可以访问。注：如使用虚拟宿主用户，在该项目设定为NO的情况下所有虚拟用户将无法访问
chroot_list_enable=YES //使用户不能离开主目录
ascii_upload_enable=YES
ascii_download_enable=YES //设定支持ASCII模式的上传和下载功能
guest_enable=YES //设定启用虚拟用户功能
guest_username=ftp //指定虚拟用户的宿主用户
user_config_dir=/etc/vsftpd/vuser_conf //设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径
```

# 4、 创建一个新的FTP用户

## 4.1 创建一个新的测试用户

```
sudo adduser testuser
sudo passwd testuser
```

### 4.2 添加测试用户到可登录用户列表文件中

```
echo  "testuser"  | sudo tee –a /etc/vsftpd/user_list
```

### 4.3 为新增的用户创建目录并设置权限

```
# 为testuser用户创建文件上传目录
sudo mkdir –p /home/testuser/ftp/upload

# 权限设置
sudo chmod 550 /home/testuser/ftp

sudo chmod 750 /home/testuser/ftp/upload

# 修改ftp目录及其子目录的用户、用户组为 testuser
sudo chown –R testuser: /home/testuser/ftp
```

# 重启vsftpd服务

```
systemvsftpd restart
```

#  最后新建ftp连接到ftp服务器

　![img](https://images2015.cnblogs.com/blog/449486/201701/449486-20170106204758144-616594963.png)


# 参考链接

1. [[centos下开启ftp服务](https://www.cnblogs.com/IT--Loding/p/6257685.html)](https://www.cnblogs.com/it--loding/p/6257685.html)
2. [Linux之CentOS7如何安装VSFTPD（FTP）服务](https://www.timewentby.com/system/linux/centos/714.html#toc-8)
