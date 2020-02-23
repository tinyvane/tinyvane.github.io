---
title: 使用GIT管理发布阿里云CENTOS网站代码
date: 2016-06-17 05:47:19
categories: 折腾
tags: [git, aliyun, centos]
urlname: update_aliyun_centos_web_code_by_git
---

## 文章更新

20160616-第一次更新
20160621-添加了SSH免密码连接的方式<!-- more -->

## 服务器安装git

我的阿里云服务使用的centos 6.5 x64，先更新yum

``` bash
yum update
```

centos 6以上的yum已经有了git源，所以不用担心，直接安装

``` bash
yum install git
```

查看git版本

``` bash
git --version
```

目前版本是1.7.1，然后配置用户名和密码

``` bash
git config --global user.name "testuser"
git config --global user.email "testuser@example.com"
```

查看安装后的信息

``` bash
cd ~
cat .gitconfig
```

或者使用下面一条命令

``` bash
git config --list
```

## 本地开发机器上创建git目录

``` bash
git init repo.git
```

这个repo用来以后放调试代码。

## 在centos服务器上

### 创建git管理账号

第一步，先创建git账号，方便管理。关于Linux的所有用户和用户组的，可以参考这篇文章《[LINUX下的用户管理](http://www.wuliaole.com/post/user_management_in_linux)》。

``` bash
sudo groupadd git #新建一个git用户组
sudo useradd git -m -s /sbin/nologin -d /home/gits -g git #新建一个git用户，创建目录/home/gits，并禁止shell登录，添加到git用户组
```
> 当在创建一个新用户user时，若没有指定他所属于的组，Linux就建立一个和该用户同名的私有组

解释一下，-m表示创建用户主目录，即如果-d后面的目录不存在，这里就是创建的意思，-s是指定用户的登录shell，这里写-s /sbin/nologin 表示用户无法使用ssh登录，-d表示该账号的主目录， -g表示将该用户加入git组

如果希望恢复git用户bash登录，可以使用 `sudo usermod -s /bin/bash username` 。

是不是看得有点云里雾里的？其实我也有点糊涂，所以特意自己整理了一篇关于linux用户管理的帖子，大家可以移步这里。

然后把上面的几个命令，拆解成了多句话，虽然不够简洁，但是意思就容易明白多了。

``` bash
groupadd gits #添加gits用户组
useradd git1 -s /sbin/nologin -d /home/git -g gits #创建用户git1，并且添加到gits组，指定其主目录为/home/git目录
```

### 创建git远程仓库

第二步，新建一个git仓库，这个仓库叫“git bare repository”，git裸仓库。

``` bash
cd /home/git
chown -R git1:gits git #改权限
cd git
git --bare init
```

这个仓库和本地的那个不一样，可以发现本地的那个仓库有在project下有一个.git的目录，而project下还有自己的代码。但是这个git裸仓库它没有自己的project，他只有和本地.git目录下一样的内容。这个git目录主要是用来保存用户git的更新的，而不是保存代码的。

### 使用post-receive脚本更新代码

第三步，当客户端push到服务器来时，自动更新网站部署的工作目录。

``` bash
$ mkdir /alidata/www.xiaowei.com #网站的目录地址
$ vim hooks/post-receive #新建目录以及文件，输入以下内容
```

``` bash
#!/bin/sh
GIT_WORK_TREE=/alidata/www/xiaowei.com git checkout -f
```

为上述脚本添加可执行权限

``` bash
$ chmod +x hooks/post-receive
```

`post-receive` 这个脚本在提交文件到git仓库时，会运行文件内的代码，所以通过这样的方法，我们在客户端push提交代码后，就能自动更新网站的文件了。

## 本地仓库和远程仓库关联并推送

在本地开发电脑上，建立本地git目录

``` bash
mkdir xiaowei.com
cd xiaowei.com
git init
git clone ssh://git1@www.zenmeban.com:22/home/git #注意，端口号22后面不能有冒号
```

会看到提示 `warning: You appear to have cloned an empty repository.` 不用管，因为本来远程仓库就是空的。

建立关联

``` bash
git remote add origin ssh://git1@www.zenmeban.com:22/home/git
touch README
echo "git test" >> README
git add README
git commit -m 'first README file'
git push origin master
```

会问你git1用户的密码，这个时候需要在远程服务器上使用passwd为git1指定个密码，否则这个用户还处于锁定状态。

如果遇到了下面的错误

``` bash
error: insufficient permission for adding an object to repository database ./objects
fatal: failed to write object
error: unpack failed: unpack-objects abnormal exit
To ssh://git1@www.zenmeban.com/home/git
 ! [remote rejected] master -> master (n/a (unpacker error))
error: failed to push some refs to 'ssh://git1@www.zenmeban.com/home/git'
```

说明git1权限不足，通过ls -all查看git目录，发现很多用户是属于root的，可执行chmod给git1分配权限

``` bash
cd /home/git
sudo chown -R git1:gits *
```

好了，现在再此push，看到下面的提示

``` bash
Writing objects: 100% (3/3), 203 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote: error: git checkout-index: unable to create file README (Permission denied)
To ssh://git1@www.zenmeban.com/home/git
* [new branch]      master -> master
```

说明/home/git可以写了，但是check out部分，也就是写入/alidata/www/xiaowei.com(你的开发目录)有问题，一看就是权限问题。

再次使用chown把你的工作目录的权限分配一下就可以了。

然后，本地添加个新文件，然后重新push一下，完美。

## 使用ssh免密码连接git库并且推送

生成一对儿rsa公钥密钥

``` bash
ssh-keygen -t rsa
```

三次输入回车，完事。

这里有一个重要的问题，需要总结一下，你在某个git目录下生成的ssh key，生成的用户名就是那个，比如我在xiaowei.com这个目录下，git保存的账号是git1。

``` bash
Your identification has been saved in /home/git/.ssh/id_rsa. #私钥保存位置
Your public key has been saved in /home/git/.ssh/id_rsa.pub. #公钥保存位置
```

需要把公钥复制到服务器上的.ssh文件里面去。

这里学习一个新的命令 `scp`。

``` bash
scp -P port /home/user/filename user@serverip:/home/user/dirname
```

以上端口中：

1. `P` 为参数，port端口。这里要注意，因为 `ssh` 命令后面的 `ssh -p229` 这里的p是小写的，而scp命令里的P是大写的。
2. user 为 `ssh user`。
3. serverip 为远程服务器ip或者域名。
4. /home/user/filename 为远程服务器的文件名。 
5. /home/user/dirname 为本地服务服务器的目录名字。

该命令的作用就是将本地的文件复制到远程的服务器地址。

``` bash
scp -P 22 /home/git/.ssh/id_rsa.pub git1@www.zenmeban.com:~/.ssh
```

通过ssh-keygetn生成一堆新的rsa钥匙，注意，远程服务器需要在对应用户的.ssh目录下的authorized_keys文件里，写上本地要连接的用户对应的公钥。

然后，可以通过 `ssh -i ~/.ssh/git1_rsa git1@www.zenmeban.com` 检测你的连接是否成功。

另外还可以通过 `ssh -vT git1@www.zenmeban.com` 调试模式来看看错误出在哪里。

这里正好介绍一下本地`.ssh`目录下的`config`文件，这个文件可以告诉ssh去哪里找私钥，如果不配置好这个文件，ssh连接是无法正确免敲密码的。

``` bash
Host github
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/github
Host coding 
  HostName git.coding.net
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/coding
Host aliyunroot
  HostName www.zenmeban.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/aliyun_root
  User root
Host aliyungit1
  HostName www.zenmeban.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/aliyun_git1
  User git1
```

一段配置对应一个要连接的远程域名，当你要通过不同用户连接同一个远程服务器的时候，就像上面的配置项3和4中那样，就需要通过一个User配置项，来区分不同的用户名，以便ssh可以找到不同的私钥了。

比如，`ssh aliyunroot` 就会直接使用git1用户连接阿里云的服务器了。

好了，GAME OVER。

## 参考文章

1. [How to Install Git on CentOS 6](http://www.liquidweb.com/kb/how-to-install-git-on-centos-6/)
2. [使用git同步管理自己的网站](http://www.chenyudong.com/archives/git-sync-manage-website.html)
3. [How To Set Up Automatic Deployment with Git with a VPS](https://www.digitalocean.com/community/tutorials/how-to-set-up-automatic-deployment-with-git-with-a-vps)