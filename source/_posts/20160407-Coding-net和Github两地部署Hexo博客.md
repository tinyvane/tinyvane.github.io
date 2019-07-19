---
title: Coding.net和Github两地部署Hexo博客
date: 2016-04-08 12:55:38
categories: Hexo
tags: [coding.net, hexo, github, ssh]
urlname: how_to_deploy_hexo_on_both_coding_and_github
---

## 文章更新

1. 20160408-初次成文
2. 20161201-文章内容调整，把hexo d卡住的具体内容，改而增加了到其他文章的链接

## 为什么会有这篇文章

github在墙外，虽然目前速度不错，但是不好说哪天就访问不了了，那国内用户咋办呢，所以就想把hexo同时推到github和国内的托管平台，目前选了码市（coding.net），因为博客的Markdown源文件都存在了coding.net的私有仓库中，所以觉得顺手就不想折腾了。

## 实现过程

### 修改_config.yml

这个地方的修改较为简单，如果你之前已经部署在了github，加一行部署到coding.net的就可以了，但是要注意格式，因为yml语法要求非常严格。

``` yml
deploy:
  type: git
  repo: git@codingnet:wuliaole/wuliaole.git
  branch: master
  message: "Coding.net with wuliaole pushlished succeed! Congrats!"
```

具体修改见下图。

![config.yml修改](configyml.jpg)

### 在coding.net建立公开项目用来存储静态文件

![coding.net建立公开项目用来存储静态文件](codingnetconfig.jpg)

### 在coding.net建立公开项目用来存储静态文件

![初始化项目](codingnetconfig2.jpg)

### 在Source目录下生成Staticfile文件

``` bash
cd source
touch Staticfile
```

注意文件名的大小写不要错。

### 开启coding.net的pages服务

![开启pages服务](startpagesservice.jpg)

因为开启演示，还需要web hook并且每天收费0.01元？并且演示方式无法绑定域名，只能使用`项目名称.coding.net`的方式，因为我采用了pages的方式。

找到Coding.net左侧的`代码`-`PAGES`服务，右侧选择分支(一般是MASTER)，点击开启，等几秒钟就好了。

### 几个错误

#### github上传时出现error: src refspec master does not match any

这是因为当前仓库下没有文件，`git add .`添加一些文件再commit再push就好了。

#### public key denied

使用`ssh -vT git@github.com`以及`ssh -vT git@coding.net`

![测试SSH是否可以连接上远程仓库](githubkeytest.jpg)

测试是否可以正确连上远程仓库，如果不行，参考下面的解决办法

#### hexo d卡住

这个问题我搜索了一下，好像没有人遇到，我等了几分钟，ctrl+c（mac下用control+c）结束了，然后又一次depoly了之后去撸了一盘，发现错误还是权限的问题。

![config.yml修改](githubautherror.jpg)

这个地方当时挺奇怪的，因为之前往coding.net远程仓库单独push的时候，都是手动输入账号和密码的，没有把ssh key添加到coding的设置里去，只单独添加到了github中，然后，这里就遇到问题了，只能以为是当推送多个git的时候，不能一个输入账号密码，一个用ssh key认证，必须要把本机的ssh key填写到多个git的设置中才可以。

所以，这里要再次将本地生成的sshkey添加到两个托管仓库中去（分别是coding.ent和github.com）。

下面用一张图片解释如何生成sshkey。

![config.yml修改](githubaddkey.jpg)

然后，在c盘（windows环境）下的用户目录下，这里是`c:\users\tinyv\.ssh`(tinyv是我的账户名，这里要换成你的账户名)，会看到有了known_hosts，以及id_rsa、id_rsa.pub等三个文件，将id_rsa.pub公钥的内容，复制黏贴到coding.net和github中去即可。

![config.yml修改](filesinsshfolder.jpg)

## 参考资料
1. []()
