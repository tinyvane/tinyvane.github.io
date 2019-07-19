---
title: hexo在github部署卡住的奇怪问题
date: 2016-08-15 16:30:19
categories: Hexo
tags: [hexo, git, github, deploy]
urlname: the_lag_problem_for_hexo_deploying_on_github
---

## 文章更新

1. 20160815-初次成文
2. 20160831-增加了一个检查的步骤
3. 20170222-更新hexo推送到多个github repo的一个小插曲

## 为什么会有这篇文章

最近电脑拿去送修了，修好了之后什么都没有了，重新`clone`了自己在coding上的hexo源文件，`hexo clean`, `hexo g`, `hexo d`，本以为一切正常，却发现hexo在显示 `On branch master nothing to commit, working tree clean` 之后就一直卡着不动，经过了小半天的时间查找问题，才解决。<!-- more -->

## 问题描述

`Hexo deploy`的时候卡住，如果使用`hexo deploy --debug`，则会显示 `Error: Permission denied (publickey). fatal: Could not read from remote repository.`

但是，key 已经设置正常，并且 `ssh -T git@github.com` 连接正常。

## 解决办法

### 步骤1：检查`_config.yml`

先说说我自己的情况，我是github和coding.net两地部署的hexo静态文件，Markdown原始文件保存在了coding.net的私密库中，使用git来管理源文件，使用`hexo deploy`将生成的静态文件推送到（部署到）github pages和coding.net两个网站的静态空间上去。

那么，我首先就是检查自己hexo网站的`_config.yml`文件中关于deploy的选项是否正确。

``` json
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
- type: git
  repo: git@github.com:tinyvane/tinyvane.github.io.git
  branch: master
  message: "Github Site updateed: {{ now('YYYY-MM-DD HH:mm:ss') }}"
- type: git
  repo: git@git.coding.net:tinyvane/tinyvane.git
  branch: master
  message: "Coding.net Site updateed: {{ now('YYYY-MM-DD HH:mm:ss') }}"
```

这是我的相关配置，网上也有把两个repo写在一起的情况，其实都是可以的，就好像下面这样

``` json
deploy:
  type: git
  repo:
    github: git@github.com:tinyvane/tinyvane.github.io.git,master
    coding: git@git.coding.net:tinyvane/tinyvane.git,master
```

主要注意2个地方就好了，每个项目之后必须接一个半角的冒号，并且冒号之后接一个半角的空格。这是严格的规定。

最新更新，关于那个host关键字，最近有了新的认识，那个地方并不是简单地一个标志而已，而是可以像`ssh -vT git@github1`使用，是不是感觉有点意思？为什么我会发现这个区别，因为最近我遇到了一个不大不小的问题，我最近使用hexo来写博客，然后在_config.yml文件中，写了两个repo地址，像如下这样

``` json
deploy:
- type: git
  repo: git@github.com:tinyvane/tinyvane.github.io.git
  branch: master
  message: "Github Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"
- type: git
  repo: git@git.coding.net:tinyvane/tinyvane.git
  branch: master
  message: "Coding.net Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"
```

这样看似没什么问题，但是如果使用`ssh -vT git@githbu.com`就会显示连接错误，如果使用`ssh -vT github`就会显示连接正确，是不是发现问题了？对了，那个host的关键字，并不是简单区别不同ssh连接的关键字而已，而是可以像上面那样使用简称，所以，我就在_config.yml中稍微修改了一下repo的地址，变成了下面这样

``` json
repo: git@github:tinyvane/tinyvane.github.io.git
```

这样就OK了，感觉又学了一招。

### 步骤2：检查`.ssh`目录配置

因为使用的git的推送方式，这种方式不需要每次部署的时候都键入账号密码，所以比较方便。因此就要好好的检查下`.ssh`目录下的文件内容和权限配置。

#### 文件内容配置

使用`ssh-keygen -t RSA` 的命令来生成公钥和私钥，主要文件名要起得有意义一些，否则默认都是`id_rsa`，比如，我采用了网站+用户名的组合，比如我的树莓派有root和pi这2个账户，对应的用户名就分别是raspberry_root和raspberry_pi。

然后，多个不同的公钥和私钥文件，以及对应的ssh端口，都需要通过config文件来妥善指定。关于这个话题，请看我的另外一个帖子，[《关于ssh免密连接的那些事》](http://www.wuliaole.com/post/how_to_ssh_server_without_inputing_the_password)，里面给出了详细的说明和例子。

#### 密钥文件权限

这个地方，其实并不容易出问题，经常在git链接的时候，会遇到提醒，说你的ssh密钥文件权限过大，存在不安全因素。这里就顺便说一下。

``` bash
sudo chmod 600 ~/.ssh/id_rsa        #更改私钥文件权限为600
sudo chmod 600 ~/.ssh/id_rsa.pub 	  #更改公钥文件权限为600
sudo chmod 644 ~/.ssh/known_hosts	  #更改known_hosts文件权限为644（这个文件存在被需要连接的服务器上）
sudo chmod 755 ~/.ssh 				      #更改.ssh目录权限为755
```

并且，需要注意这些目录的拥有者是不是当前用户，如果是非管理员想使用root的文件，也会遇到错误。

### 步骤3：检查git版本

在网上google了几个小时，也没有解决这个文件，最终在很多条回复中发现有人提到了git自身可能存在问题。经过试验，我遇到的问题应该也是git的问题，经过升级git到比较新的版本，解决了问题。

我的系统是OSX EI CAPITAN，通过`git --version`，发现自己的版本是2.7.3，有点老了，如何升级呢？

当然是建议用brew安装了，目前我装的是2.9.3

``` bash
brew install git
```

然后，2.7.3的老版本git需要删掉么？如何让新装的git覆盖老的git？

我这里建议是在 `~/.bash_profile` 文件中，加入以下内容：

``` bash
export PATH=/usr/local/bin:/usr/local/sbin:${PATH}
```

这样可以让bash优先搜索 `/usr/local` 下的指令，而且不会覆盖老文件，比较安全。

### 步骤4：检查'git remote -v'

这个步骤，也是我最近发现的，而且严格来说，因为`hexo d`和`git push`并没有什么关系。

而且目前也不能确定WIN10导致的这个问题，在另外一台WIN10的PC上，虽然`ssh -T git@git.coding.net`可以顺利得到欢迎信息，但是每次`git push`的时候，依然会问我账号密码，所以我觉得应该是哪里设置有问题。

直到我从网上的一个帖子[《git使用ssh密钥》](http://chen.junchang.blog.163.com/blog/static/634451920121199184981/)，获得了一点灵感，在这个帖子里，介绍了如何使用`git remote set-url`来修改仓库的push是使用git还是https方式。具体方法如下：

查看目前的远程仓库推送和拉取方式

``` bash
git remote -v
```

如果返回值为空，说明你的`.git/config`文件缺少 `[remote "origin"]` 内容，需要使用下列的命令进行添加

``` bash
git remote add origin [url]
```

这是最简单的，如果返回值不是空，你想修改remote库的链接的话，可以使用

``` bash
git remote rm origin 先删掉
git remote add origin [url] 再添加
```

或者用另外一个办法 git remote set-url origin [url] 这样也可以修改。

  一个小技巧，如果你不想每次都输入git pull origin master或者push origin master的话，可以用下面的命令，让git默认推送或者拉取特定的分支

  ``` bash
  git config branch.master.remote origin
  git config branch.master.merge  refs/heads/master
  ```
  
  这样就可以用简化的git push或者git pull来推送或者拉取内容了。

这样就有了内容，如果你发现`git remote -v`返回的是https方式，那么需要修改为git方式

从https修改为git方式

``` bash
git remote set-url origin git@git.coding.net:tinyvane/hexo.git
```

这样再次运行`git pull/push`就不会在要你密码了。

也可以通过下面的命令，从git修改为https方式

``` bash
git remote set-url origin https://git.coding.net/tinyvane/hexo.git
```

另外，还有一个比较奇怪的问题是，我在rMBP下，无论是`hexo deploy`还是`git push`，都不需要输入密码，而且`git remote -v`显示的也是https方式。所以，就更让我纠结了。

顺便说一下，虽然没有哪个文件记录remote的通信方式，但是这个确实在第一次`git clone`的时候就定下来了，`git clone`的时候使用的https的话，就会默认使用https来负责remote仓库的push和fetch。如果想手动该修改，去你本地仓库下`.git`目录下修改 `config`文件，其中有一段`[remote]`记录了这个方式。

## 其他：如何查看密钥对应的md5码

这算是题外话了，写下来放着留着以后看。

错误：`Github permission denied: ssh add agent has no identities`

使用 `ssh-add l`（openssh 版本6.7及以下，如果更新的版本，请使用 `ssh-add -l -E md5`命令）想查看公钥对应的md5码，但是遇到了上面的错误，就要使用 `ssh-add /path/to/my-ssh-folder/id_rsa`，把对应的私钥文件添加到`ssh agent`中去，然后就可以了。

PS：目前还不是很明白`ssh agent`是干嘛用的。

## 参考文章

1. [Mac OS X Lion 下 Git 如何升级？](https://segmentfault.com/q/1010000000095119)
2. [Fixing “WARNING: UNPROTECTED PRIVATE KEY FILE!” on Linux](http://www.howtogeek.com/168119/fixing-warning-unprotected-private-key-file-on-linux/)
3. [hexo官方网站上的一个类似问题的解决过程](https://github.com/hexojs/hexo/issues/1478)
4. [git使用ssh密钥](http://chen.junchang.blog.163.com/blog/static/634451920121199184981/)
