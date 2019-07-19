---
title: 从WordPress迁移到Hexo
date: 2016-03-10 19:56:40
categories: 
- Hexo
- 基础
tags: [Hexo, WordPress, 迁移, mac, windows, node.js, npm]
urlname: from_wordpress_to_Hexo
---

> FOOLMAN喜欢简单的东西，有时候为了达到一个简单目标，却需要经历复杂无比的过程

## 文章更新

1. 20160310-初次成文
2. 20160704-已经过去了4个月，更新了MAC下的Hexo安装方法
3. 20160808-修改完善mac下的安装办法和步骤
4. 20160902-增加了错误排查以及如何更换HEXO模板的内容

## 为什么会有这篇文章

WordPress确实是个优秀的博客系统，但是却越来越臃肿，平心而论，如果身在墙外，肯定会一直用下去，谁想天天没事瞎折腾呢。然而，WP却变得越来越臃肿，插件总需要升级，功能还要和PHP以及LINUX不停的大叫道，经常的头大。

这两天自己的WordPress博客再次无缘无故打不开了，即便装了`Disable Google Font`插件也是没有改善，用Chrome的开发者工具也没发现什么不妥，虽然有处女[座]情节的我应该一究到底，但是还是觉得KEEP THINGS SIMPLE比较重要，陷在复杂的事情里面，自己也变得复杂了。

在网上逛久了额，不经意间总会看到一些博客下面写着一句话“由Hexo强力驱动”！当时就心想，这是什么鬼？？不过也没多想。

然而，当这两天WordPress再次无法打开之后，还是决定换个博客系统吧，经过gogole，大概知道Hexo是什么，纯静态博客，一番了解之后，简约不简单，如此概括。

> 今天在上班的路上我还在想，也许过不了多久，Hexo也会同样变得臃肿，也许就有另外一种更加简单的博客框架出现，不过管他呢，折腾一下没坏处。

先说这个HEXO，其实建立博客的并不是唯一的方案，其他还有Ghost和其一些方案。Hexo只是因为他的简单，简洁，比较流行，大家可以自由选择其他方式搭建自己的静态博客，静态有什么好的？速度飞快！还有呢？这个理由还不够么？

> 我喜欢写流水账似的记录，因为我觉得只有自己消化了的东西，写出来的心得，才能是精华，才值得记录。

可能自己写作习惯不好，经常偷懒，很多东西就算记下来了，也是随手扔在印象笔记中，懒得整理了。不过既然有了这个契机，可以适当将笔记中的资料整理一下写成博客。

## 如何在Windows系统上安装Hexo

Hexo本身就是一个由 Node.js 驱动的框架，N多包（至少我这样认为），可以识别 Markdown 格式，并且转换为HTML页面，这样，就可以借助GitHub或者Coding.ne的Pages服务，来展示站点内容，不但安全性一流，还能利用Github或者Coding.net这些大站的速度，还有一点，免费不操心。

在电脑上要装的，主要是 Git 和 Node.js 这两个工具。

### 安装GIT

去[Git for Windows官网](https://git-scm.com)下载并安装。

### 安装Node.js

去[Node.js官网](https://nodejs.org)下载并安装。

注意：初次折腾，尽量使用默认路径，并且避免路径中出现中文字。

### 安装Hexo

上面两个都是软件，下载了即可，我下载的Git版本是2.7.2 64位，Node.js是5.8.0 稳定版，也是64位版本的。

安装好了这两个软件之后，在你的硬盘上，新建一个目录，作为你Hexo博客的仓库，我这里是f:\Hexo，然后进去这个目录，点右键，选择`Git Bash Here`

![Git Bash Here](gitbashhere.jpg)

![Hello the Git](hellothegit.jpg)

如果你熟悉Windows的CMD工具，那么你对GitBahsh的界面一定不会陌生，这其实是个linux下的命令行工具，因为linux下默认就是这样，可能你会兴奋，也许你会感觉郁闷，不过没办法，硬着头皮，跟着我一步一步做下去吧。

然后，在这个窗口输入下面的命令

``` bash
npm install -g hexo-cli #-g表示全局安装
```

先不解释什么时候npm，先说这句命令，可能会运行很长时间，如果你觉得出错了，可以用`CTRL+C`来结束，不过更靠谱的，是加一个`--verbose`参数，变成这样，这样你就可以看到整个安装过程了，这命令对于其他命令也适用。

``` bash
npm install -g hexo-cli --verbose
```

这里分享一个小插曲，我在WIN10系统上安装的，所以遇到了两个WARN信息：

``` bash
npm WARN optional Skipping failed optional dependency /browser-sync/chokidar/fsevents:                            
npm WARN notsup Not compatible with your operating system or architecture: fsevents@1.0.8 
```

解释：fsevent是mac osx系统的，所以在windows系统下会遇到这个警告，忽略即可（更新：我后来在MAC上安装的过程中，也看到过类似的WARN，所以。。。谣言不靠谱）。

好了，Hexo安装完成后，使用`mkdir folder`命令建立一个用来存放hexo的目录，进入该目录，并且输入下面的命令

``` bash
Hexo init
```

> 注意，如果你不是第一次安装，而是要在其他PC或者MAC上同步之前的环境，跳过上面这句。

然后进入你创建的目录，输入 `npm install`，让npm安装Hexo依赖环境，就是只装必要的文件，其他Hexo的完整包，还在npm的全局那呢，记得么？这点比较重要哦。

好了，如果你输入`Hexo -v`查看Hexo版本可以看到结果，说明Hexo安装完毕。

### 启动Hexo服务器

一句话

``` bash
Hexo server
```

或者缩写成`Hexo s`也一样。然后，你的界面会变成下面这样

![Hexo Server](Hexoserver.jpg)

这个界面不会再出现提示符$了，因为服务一直运行着，除非你按了`CTRL+C`。

现在服务已经好了，准备开始写你的一篇博客吧。

### 第一篇博客

继续上面的说，当你的Hexo服务运行之后，那个窗口不要关闭，因为你关闭了，也就等同于`CTRL+C`关闭了Hexo服务。

如果关闭了Hexo服务，请重新在Hexo目录右键选择Git Hash Here，重新开一个Git窗口。

输入下面的命令

``` bash
Hexo new "你的第一篇博客"
```

后面括号里是你的日志的名字，可以是中文或者英文，如果有空格，也没问题。输入这个命令之后，去Hexo目录下，找source目录，里面有一个_post目录，打开就能看到你的日志了，就是一个文本文件。

你可以用任何编辑器编辑这个文件，比如记事本、Notepad++、Sublime Text等等，我之前喜欢用Notepad++，现在转用了Sublime Text 3，因为后者好像作为Markdown标记的编辑器更为方便一些。

这时候，如果你打开浏览器，输入 `http://localhost:4000`，就可以看到你的博客了。

说起来用Markdown标记写博客，还是有很多需要学习的地方，我刚开始的时候，很简单粗暴，打开这个[作业部落](https://www.zybuluo.com/mdeditor)的在线Markdown编辑器放在一边，一边参考，一边学习他的标记符号。

![作业部落的Markdown实例页面](zuoyebuluomarkdown.jpg)

### Hexo常用命令

``` bash
Hexo help #查看帮助
Hexo init #初始化一个目录
Hexo new "postName" #新建文章，可以缩写为hexo n
Hexo new page "pageName" #新建页面
Hexo generate #生成网页，可以在 public 目录查看整个网站的文件，可以缩写为hexo g
Hexo server #本地预览，'Ctrl+C'关闭，可以缩写为hexo s
Hexo deploy #部署.deploy目录，可以缩写为hexo d
Hexo clean #清除缓存，**建议每次执行命令前先清理缓存，每次部署前先删除 `.deploy` 文件夹**
```

同时Hexo支持命令的组合：

``` bash
Hexo d -g #先重新生成博客内容，再部署推送到静态空间
Hexo s -g #先重新生成博客内容，再在本地生成服务器，可以进行预览
```

## 在OSX下安装Hexo

其实，再回头看这个帖子，时间已经过去4个月了，真是有点惭愧了，不过也可以说明，我用Hexo写博客，不知不觉已经这么长时间了。

好了，废话不多说，直接进入正题。

### 使用Homebrew安装node

MAC下的使用，绕了很多弯路，从最开始用Node.js的安装包安装Node，到现在使用Homebrew来安装，变化很大。也让我对linux系统越来越有好感。

好了，废话不多说，先从homebrew安装Node.js开始，Node.js自带npm。

``` bash
brew install node.js
```

如果上面的命令错误，说明你还没有在mac下安装过Homebrew，如何安装Homebrew以及常用brew命令，请移步[这里](http://www.wuliaole.com/post/the_tutorial_101_of_homebrew_on_mac)

~~需要注意的是，Hexo的安装位置我是装在了/usr/local/lib/node_modules下，才安装成功的。其实这个位置，我现在还是有点疑惑，因为对那些Link产生的文件，连接的对象还不是很明白。~~

~~cd /usr/local/lib/node_modules~~
~~npm install Hexo -no-option~~

### 安装Hexo-cli

npm安装好之后，那些warn不用理会，直接安装Hexo，命令也是一句话。

``` bash
npm install Hexo-cli -g
```

然后通过命令，`Hexo -v`，如果看到了hexo的版本号，说明安装成功。

## Hexo主题、插件以及其他内容

Hexo的安装并不麻烦，基本就是需要Node.js、NPM、GIT这几样东西，在Mac因为是原生linux系统，所以问题不大，而Windows 10也自带bash了，虽然对中文目前支持非常稀烂，但是未来应该有所期待。并且其实Windows下的linux shell也非常多，比如babun，cygwin等，而且上文介绍安装过程中，涉及的[Node.js官网](https://nodejs.org)安装后，也带了一个bash shell。好了，废话不多说了，继续说hexo使用过程中的一些其他需要注意的问题。

先建立一个目录，然后进去，这里有一点需要说明的是，如果你是第一次使用Hexo写博客，使用命令

``` bash
Hexo init foldername #建立的Hexo文件夹名字
```
然而如果你是为了同步其他电脑上的Hexo博客内容，直接建立一个空目录，并且不要执行 `hexo init` 操作，否则Hexo会将其初始化，并且无法继续同步其他内容了。

### Hexo安装主题

上面步骤二选一，然后继续

``` bash
git clone https://git.coding.net/tinyvane/Hexo.git foldername #这里更改成你远程hexo仓库的名字
cd foldername
#让npm安装必要的Hexo组件
npm install
#安装Hexo-deployer-git ，部署Hexo用
npm install Hexo-deployer-git 
#（可选）安装 Hexo-asset-image 和模板，确认 _config.yml 中 `post_asset_folder:true` 。
npm install hexo-asset-image --save
#使用submodule的方式添加next主题
git submodule add https://github.com/iissnan/Hexo-theme-next themes/next
#或者其他主题
git submodule add https://github.com/tinyvane2/hexo-theme-hueman.git themes/hueman #https方式

git submodule add git@github.com:tinyvane2/hexo-theme-hueman.git themes/hueman #ssh方式，二选一
```

> 如果遇到了错误`Receiving “fatal: Not a git repository” when attempting to remote add a Git repo`，说明当前目录还不是一个由GIT管理的项目，请先使用命令`git init .`对当前项目进行初始化。

如果要了解更多关于多终端同步Hexo更新博客，请参考我的另外一个帖子，地址在[这里](http://www.wuliaole.com/post/sync_Hexo_among_different_clients)。

## Hexo 更换主题

hexo默认带的主题是landscape，像极了WordPress的默认主题，因此，我非常有冲动要换一个。

### 有哪些主题可以让你选择？

知乎上有一个帖子，统计了很多的高星Hexo主题，详情戳[这里](http://www.zhihu.com/question/24422335)

我之前使用的是iissnan的Next主题，目前使用的ppoffice的hueman主题。

### 安装主题

* Next主题

    ``` bash
    git clone https://github.com/iissnan/hexo-theme-next themes/next
    ```

* 或者 Hueman主题

    ``` bash
    git clone https://github.com/ppoffice/hexo-theme-hueman.git themes/hueman
    ```

然后修改`站点配置`（hexo主目录下的`config.yml`文件），将`theme: landscape`修改为`theme: hueman`或者`theme: next`

### 主题生效

``` bash
hexo clean && hexo g #清理文件并且生成文件
hexo s #本地运行
```

## 几个插件

``` babsh
npm install Hexo-deployer-git #Hexo-deployer-git ，部署Hexo用
npm install hexo-asset-image --save #Hexo-asset-image 和模板，确保主题配置文件中`post_asset_folder`选项为`true`
npm install hexo-generator-json-content -S #Hueman主题中的insight search（站内搜索）功能必备插件
npm install hexo-baidu-url-submit --save #百度链接生产插件
```

PS: 需要注意的是，`hexo-baidu-url-submit`和`hexo-generator-json-content`两个插件如果直接安装，而没有安装相应的主题，会导致hexo错误，而无法正常执行，请注意。

然后浏览器中输入地址`localhost:4000`就可以看到新主题已经使用上了。

说简单不简单，这里有个坑，如果执行了上面的语句，访问localhost发现主题还是默认的，关掉服务器的那个shell，再重新打开，输入`hexo -s debug`，然后再次访问`localhost:4000`，应该就可以看到新主题已经换上了。

好了，装好了基本的hexo文件了，下面的操作，是我自己的个性设置，大家可以不用理会。因为Hexo的主题配置文件(也是_config.yml)，如果操作用submodule的方式，你是没有办法push到原作者的github库的，所以我将其保存在了主项目的 git 库中，方便保存主题配置

``` bash
cp <Hexo folder>/themes/next/_config.yml <Hexo folder>/_config_theme.yml
cd themes/next
git add .
git commit -m 'overwrite the _config.yml files from Hexo repo'
cd ../.. 	
```

如果要了解更多关于多终端同步Hexo更新博客，请参考我的另外一个帖子，地址在[这里](http://www.wuliaole.com/post/sync_Hexo_among_different_clients)。

### Hexo 更换主题

hexo默认带的主题是landscape，像极了WordPress的默认主题，因此，我非常有冲动要换一个。

## 有哪些主题可以让你选择？

我还是在知乎上看到了帖子，详情请戳[这里](http://www.zhihu.com/question/24422335)

我这里演示的iissnan的hexo-theme-next主题。至于其他的，自己选吧。

## 将主题从远程下载到本地

开Git，在命令行的地方，输入

``` bash
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

后面是你存放主题的默认目录，themes是默认的主题文件夹。

## 修改配置

你的默认hexo目录下面，有一个文件叫`_config.yml`，打开后，会看到一行`theme: landscape`，注释掉，加上一句

`theme: next`，这个next要和你themes目录下新下的主题保存的目录名称一致。

然后，执行下面的语句

hexo clean && hexo g

clean是清理，g是重新生成，然后就可以看到新主题已经使用上了。

说简单不简单，这里有个坑，如果执行了上面的语句，访问localhost发现主题还是默认的，关掉服务器的那个git，重开git，输入`hexo -s debug`，然后再次访问`localhost:4000`，应该就可以看到新主题已经换上了。

## 参考资料

1. [Sublime Text 3的使用](http://www.wuliaole.com/)
2. [如何使用GitHub Pages免费上传你的Hexo博客](http://www.wuliaole.com/)
2. [代码高亮使用说法](http://highlightjs.readthedocs.org/en/latest/css-classes-reference.html)
4. [叶阳栩宁的博客](http://iread.io/2015/09/Hexo-guide-2/)
