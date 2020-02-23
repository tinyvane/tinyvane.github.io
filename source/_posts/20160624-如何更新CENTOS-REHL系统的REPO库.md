---
title: 如何更新CENTOS/REHL系统的REPO库
date: 2016-06-24 18:18:29
categories: 折腾
tags: [centos, rhel, yum, repo, responsity]
urlname: how_to_add_new_yum_repos_to_centos_rhel
---

## 更新日期

20160624-初次成文
20180928-更新了EPEL的内容

## 为什么会有这篇文章

因为使用系统默认的YUM源，更新系统速度慢呗！我使用的阿里云的CENTOS系统，在使用 YUM 安装和更新软件的时候，速度不是很理想，因此一直在寻找更新和添加 YUM REPO[^1] 的方法，而且网上现在也有很多阿里云的国内镜像，或者是比如清华大学、重庆大学这些机构提供的 REPO 库，正好趁现在这个机会总结一下。<!-- more -->不过在继续讨论这个问题之前，先

## 基本的概念

1. 什么是REHL？

REHL是Red Hat Enterprise Linux的缩写。目前有很多个版本的Linux，比如CentOS、RedHat的RHEL、Fedora、Ubuntu、Debian等等，这些Linux大致可以分为两个系列，面向个人桌面领域，和面向服务器领域。

其实，Red Hat Linux是Red Hat（红帽公司）最早发行的个人版本的Linux，然后从9.0版本之后，红帽公司就停止了个人版本的Linux，或者可以说是个人桌面版，而是将企业的精力转向了服务器版本的开发上，也就是推出了RHEL。

而原来的个人版的REDHAT LINUX，则由来自开源社区的Fedora计划合并，成为Fedora Core发行版本。

至此，Red Hat Linux则分为两个系列，分别是由RedHat公司提供收费技术支持和更新的Red Hat Enterprise Linux，也就是上面所说的RHEL，以及由开源社区支持的Fedora Core。

2. 其他的Linux都有什么？

上面说了Fedora和RHEL，此外，还有CentOS，Debian，Ubuntu等。

CentOS不是Redhat的，但它是由Redhat的服务器版本RHEL重编译而来的，和RHEL就是对孪生兄弟。它是完全克隆的免费Redhat企业版。CentOS可以像REHL一样的构筑Linux系统环境，但不需要向RedHat付任何的费用，同样也得不到任何有偿技术支持和升级服务。其特点是面向企业级服务器应用、开源免费、各大企业服务器广泛使用、文档资料齐全、稳定、安全。

Fedora Core(缩写为FC)，上文提到过，FC被红帽公司定位为新技术的实验场地，许多新的技术都会在 FC 中检验，如果稳定的话，红帽公司则会考虑加入 Red Hat Enterprise Linux 中。由于版本更新频繁，性能和稳定性得不到保证，因此，一般在服务器上不推荐采用Fedora Core。其实可以这么认为，Fedora就是Red Hat发行Red Hat企业版Linux的一个实验版本，拿用户做测试，为Red Hat企业版发布做基础。
特点：适合桌面应用、稳定性较差

Debian，或者称Debian系列，包括Debian和Ubuntu等。Debian是社区类Linux的典范，是迄今为止最遵循GNU规范。Debian最具特色的是 apt-get/dpkg包 管理方式，其实Redhat的YUM也是在模仿Debian的APT方式，但在二进制文件发行方式中，APT应该是最好的了。Debian的资料也很丰富。

Ubuntu，基于Debian发行版和GNOME桌面环境.它使用Bash作为基础Shell，所以在很多基础命令上，ubuntu与CentOS的差别不是很明显，而ubuntu在桌面界面上要做的更为出色。其特点是面向桌面应用、界面美观、操作简单。

2. 什么是Repo？

Repo是Repository的缩写，Repository是仓库/项目的意思，通常简写为repo，但是这个单词，在linux下，和在git下的含义并不完全相同。git下的含义这里就不讨论了。

在linux下，repo是yum源的配置文件，一个repo文件定义了有ige或者多个软件仓库的细节内容，例如我们将从哪里下载需要安装或者升级的软件包，repo文件中的设置内容将被yum读取和应用！

3. 什么是yum？

YUM是 Yellowdog Updater Modified 的简称，通过命令方式解决LINUX系统下RPM包的安装工具，是CENTOS、RHEL系统下安装更新软件的利器，解决了rpm安装包的依赖性问题，为使用者和程序开发者提供了不小的便利，YUM基于 GNU（General Public License）协议授权公布，YUM在安装软件包之前，会通过检测在线REPO库内的软件依赖关系，从而节省用户自己分析依赖项的辛苦。

4. yum是如何工作的？

YUM的工作原理并不复杂，每一个 RPM软件的头（header）里面都会纪录该软件的依赖关系，那么如果可以将该头的内容纪录下来并且进行分析，可以知道每个软件在安装之前需要额外安装哪些基础软件。也就是说，在服务器上面先以分析工具将所有的RPM档案进行分析，然后将该分析纪录下来，只要在进行安装或升级时先查询该纪录的文件，就可以知道所有相关联的软件。所以YUM的基本工作流程如下：

服务器端：在服务器上面存放了所有的RPM软件包，然后以相关的功能去分析每个RPM文件的依赖性关系，将这些数据记录成文件存放在服务器的某特定目录内。

客户端：如果需要安装某个软件时，先下载服务器上面记录的依赖性关系文件(可通过WWW或FTP方式)，通过对服务器端下载的纪录数据进行分析，然后取得所有相关的软件，一次全部下载下来进行安装。

5. 常用的repo（仓库）都有哪些？

目前比较常用的5个YUM库分别是 RPMForge, EPEL, REMI, ATrpms 以及 Webtatic，经过近几年的发展，阿里云也逐渐发展壮大，拥有了自己的 YUM REPO库。这几个REPO库包含的软件并不完全相同，但是足可以满足一般用户的需求。

下面的命令，分别对应 CentOS/RHEL 的版本 7/6/5，以及 32位 或者 64位 等不同版本情况，有的库甚至包含了 Fedora 系统的RPM包。

6. linux软件的安装方式？

刚才提到了RPM，rpm是红帽公司的linux的软件包格式，类似软件包的扩展名还有deb包、run包、bin包等等，都是因为上面多种不同的linux版本造成了不同的安装包的规范名称，

通常，在RHEL/CENTOS/FEDORA这些linux系统下，软件包的扩展名通常是rpm，在debian/ubuntu的linux，安装包的扩展名通常是deb。

另外，还有几种拓展名，比如run，sh，bin这些，都是安装文件的扩展名，并不严格区分linux系统，想要运行这些文件，通常先chmod给他们一个+x的权限，然后运行即可。

除了上面说的安装包或者安装执行文件的方式，还有一种就是编译的方式，但是这种方式比比较复杂，优点就是灵活度很高，比如，可以自定义软件的安装路径，安装使用的账号等各种参数，这种方式通常用于安装源代码的安装。需要一个编译的过程，然后再安装。

举例：

安装nagios插件（nagios是一种开源的监控软件，需要多种插件支持监控模块）

``` bash
cd /tmp/nagios-plugins-* #先进入目录
./configure --with-nagios-user=nagios --with-nagios-group=nagios # 配置，即安装的用户安装的路径都可在此设置，这一步一般用来生成 Makefile，为下一步的编译做准备
make #编译过程如果 在 make 过程中出现 error ，你就要记下错误代码，缺少什么包就安装什么包。
make install #如果make无问题就可以直接安装了。
```

## 比较好的YUM REPO

之前在网上搜索 LINUX 系统难题的过程中，在 CENTOS 官方论坛发现一些用户，并不推荐使用 Webtatic[^2] 的 REPO 库。其原因是 Webtatic 的库里的一些RPM包[^3]的命名和官方有冲突，但是依然发现网上很多地方提供的教程使用了Webtatic的包，没办法，也许就像之前那篇帖子里讨论的如何强制树莓派使用静态IP地址一样，很多博客更新的解决办法都存在抄来抄去的问题，我自己也不例外，不过希望自己每一个帖子，都能保持不断的更新，这也是为什么我写博客的时候，都会在冒段的地方，加入 `更新日期` 这个看似没多大用的段落，也是为了督促自己不断改正错误，能够与时俱进。

### 添加 阿里云 库

阿里云是最近新出的一个镜像源。得益于阿里云的高速发展，这么大的需求，肯定会推出自己的镜像源。阿里云Linux安装镜像源地址：`https://opsx.alibaba.com/mirror`，网站比较友好，可以看到页面的第一个就是CentOS的镜像源，然后点击查看帮助，就有了详细的安装方法。

``` bash
#CentOS 5

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo

#或者

curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo

#CentOS 6

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

#或者

curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

#CentOS 7

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

#或者

curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

### 添加 RPMForge 库

``` bash
#系统为 CentOS/RHEL 7 x86 64bit:
rpm -Uvh http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm

#系统为 CentOS/RHEL 6 x86 64bit:
rpm -Uvh http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm

#系统为 CentOS/RHEL 6 x86 32bit:
rpm -Uvh http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.i686.rpm

#系统为 CentOS/RHEL 5 x86 64bit:
rpm -Uvh http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el5.rf.x86_64.rpm

#系统为 CentOS/RHEL 5 x86 32bit:
rpm -Uvh http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el5.rf.i386.rpm
```

### 添加 EPEL 库

``` bash
#系统为 CentOS/RHEL 7 64位
rpm -Uvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm

#系统为 CentOS/RHEL 6 64位
rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

#系统为 CentOS/RHEL 5 64位
rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
```

### 添加 REMI 库

``` bash
#系统为 CentOS/RHEL 7
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

#系统为 CentOS/RHEL 6
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

#系统为 CentOS/RHEL 5
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-5.rpm

#系统为 Fedora 24
rpm -Uvh http://rpms.famillecollet.com/fedora/remi-release-24.rpm

#系统为 Fedora 23
rpm -Uvh http://rpms.famillecollet.com/fedora/remi-release-23.rpm

#系统为 Fedora 22
rpm -Uvh http://rpms.famillecollet.com/fedora/remi-release-22.rpm
```

### 添加 ATrpms 库

``` bash
#系统为 CentOS/RHEL 7 x86 64bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-7-7.el7.x86_64.rpm

#系统为 CentOS/RHEL 6 x86 64bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-6-7.el6.x86_64.rpm

#系统为 CentOS/RHEL 6 x86 32bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-6-7.el6.i686.rpm

#系统为 CentOS/RHEL 5 x86 64bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-5-7.el5.x86_64.rpm

#系统为 CentOS/RHEL 5 x86 32bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-5-7.el5.i386.rpm

#系统为 Fedora 20 x86 64bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-20-7.fc20.x86_64.rpm

#系统为 Fedora 20 x86 32bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-20-7.fc20.i686.rpm

#系统为 Fedora 19 x86 64bit
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-19-7.fc19.x86_64.rpm

#系统为 Fedora 19 x86 32bit:
rpm -Uvh http://dl.atrpms.net/all/atrpms-repo-19-7.fc19.i686.rpm
```

### 添加 Webtatic 库

centos论坛上有人对 Webtatic 的库比较反感，因为这里的程序包的命名好像和官方的不是很一致，让我对 Webtatic 的印象不是特别好。

``` bash
#系统为 CentOS/RHEL 7:
rpm -Uvh http://repo.webtatic.com/yum/el7/webtatic-release.rpm

#系统为 CentOS/RHEL 6:
rpm -Uvh http://repo.webtatic.com/yum/el6/latest.rpm

#系统为 CentOS/RHEL 5:
rpm -Uvh http://repo.webtatic.com/yum/el5/latest.rpm
```
## 生成缓存

``` bash
yum makecache
```

## 总结

RPMForge、ATrpms的repo地址连不上，Webtatic的普遍不得到推荐，所以，EPEL和REMI还有阿里云的三个装上就好了。

## 参考网站

1. [Top 5 Yum Repositories for CentOS/RHEL 7/6/5](http://tecadmin.net/top-5-yum-repositories-for-centos-rhel-systems/)
2. [常用Linux分类](http://www.51testing.com/html/00/457100-877560.html)
3. [认识repo文件](http://kpjack.blog.51cto.com/627289/128325)


## 文中的缩写说明

[^1]: repo文件是Fedora中yum源（软件仓库）的配置文件，通常一个repo文件定义了一个或者多个软件仓库的细节内容，例如我们将从哪里下载需要安装或者升级的软件包，repo文件中的设置内容将被yum读取和应用！

[^2]: webtatic是网上比较流行的一个yum库，还有其他几个如RPMForce，EPEL，REMI，ATrpms等，都是比较流行的yum库，这些库内都是软件的RPM安装包。

[^3]: RPM 是 LINUX 下的一种软件的可执行程序，你要做的就是通过rpm命令安装那些后缀名是rpm的文件包，就可以安装好某个软件了。RPM是Redhat Linux Packet Manager的缩写，后缀是rpm。