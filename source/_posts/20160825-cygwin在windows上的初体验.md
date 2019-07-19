---
title: cygwin在windows上的初体验
date: 2016-08-26 02:14:37
categories: 折腾
tags: [cygwin, linux, shell, windows]
urlname: use_cygwin_as_linux_shell_on_windows_10
---

## 文章更新

1. 20160826-初次成文
2. 20160928-更新了cygwin的坑
3. 20170313-更新了git的更新内容

## 为什么会有这篇文章

之前用过BABUN，也用过GIT+NODE.JS的组合来使用GIT，都不是特别满意，发现很多人都推荐在Windows上使用cygwin来获得linux体验。PS:不小心改动cygwin下的text编码，导致github上的文件名全部乱码了，所以就卸载了cygwin, git for windows还有node.js等等一系列东西，全部重装一次，记录一下。<!-- more -->

## 如何安装cygwin

在安装之前，网上查了一下cygwin和msysmin？的区别，还是选用了cygwin，一个是之前用的就是这个程序，另外一个是这个比msysmin要好一些？程序的下载[地址](https://www.cygwin.com/)。

值得一说的就是cygwin的package的安装方式，全部依靠setup.exe来完成，不过初次安装的时候，建议带着`git`, `wget`和`vim`。

> 我的cygwin自带的git版本是2.8，此时[官方git](https://git-scm.com/)的版本是2.12.0，如果大家想升级cygwin下的git，需要手动编译升级，可以参考这篇[文章](http://www.gizmoplex.com/wordpress/upgrade-git-to-latest-stable-release-in-cygwin/)。

## cygwin下的git设置

JiangXin的这篇[文章](http://www.worldhello.net/gotgit/01-meet-git/050-install-on-windows-cygwin.html)，对cygwin以及git设置讲解的比较实用，大家可以参考一下。我从里面摘录了几个：

1. 设置用户主目录，修改Cygwin启动的批处理文件（如：C:\cygwin\Cygwin.bat），在批处理的开头添加如下的一行，就可以清除其他软件为Windows引入的HOME环境变量。

    ``` bash
    set HOME=
    ```

2. 忽略文件权限的可执行位

    ``` bash
    git config --system core.fileMode false
    ```

3. 支持中文

    ``` bash
    git config --global core.quotepath false
    ```

## 安装node.js

因为我装cygwin目的之一是为了hexo，所以当我发现在hexo目录下，实用hexo new命令说找不到，发现缺少node和npm，网上google了一番，没啥说的，也不指望原生编译安装了， 直接去官网下了一个LTS版本，也没有网上说的需要手动添加环境变量的问题，装好了，重开一个cygwin窗口，搞定了。

## 如何用命令管理package

推荐apt-cyg来管理，命令也比较简单（这里至少需要wget或者lynx）

``` bash
wget rawgit.com/transcode-open/apt-cyg/master/apt-cyg
chmod +x apt-cyg
mv apt-cyg /usr/local/bin/
apt-cyg install vim
```

或者

``` bash
lynx -source rawgit.com/transcode-open/apt-cyg/master/apt-cyg > apt-cyg
install apt-cyg /bin
apt-cyg install vim
```

上面两种方式都可以。我发现如果直接在shell下运行 `apt-cyg` 命令是不行的，必须用

`/bin/apt-cyg install vim` 的形式，说明cygwin下的PATH应该是缺少`/bin`目录，果然通过运行 `echo $path`，返回结果为空。

### apt-cyg的参数

具体可以参考他的github官方[说明](https://github.com/transcode-open/apt-cyg)

这里大概总结一下

- install 安装package(s).
- remove 删除某个package
- update 升级setup.ini文件中所有package到最新版本。服务器地址由setup.rc定义。
- download 下载package，但是并不安装，也不升级。
- show 显示某个package的信息
- depends 生成某个package的依赖包信息
- rdepends 以树形式，生成某个package的依赖包信息
- list 可以通过正则表达匹配列出符合的package，如果直接list，则列出所有安装的package信息
- listall 列出所有package.
- category 根据目录名称列出所有下面的package
- listfiles 列出某个package的所有文件。可以给出多个package的名字。
- search 搜索某个package
- searchall 从cygwin.com提取某个package的包信息

## 安装oh-my-zsh

``` bash
apt-cyg install zsh curl git
```

为了zsh准备的三个package，如果你在安装cygwin的时候已经默认安装了这些，上面的命令可以忽略。

``` bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

遇到了错误

``` accesslog
C:/cygwin64/bin/curl.exe: error while loading shared libraries: cygmetalink-3.dll: cannot open shared object file: No such file or directory
```

说明curl少一个文件，这里可以用一个比较有用的命令 `cygcheck`，来了解curl到底缺少了什么文件。

``` bash
cygcheck /usr/bin/curl #路径换成/bin/curl也可以
```

结果发现是 `cygmetalink-3.dll`，这个文件，GOOGLE了一下，发现网易的镜像有这个文件，地址在[这里](http://mirrors.163.com/cygwin/x86/release/libmetalink/libmetalink3/)

``` bash
mkdir /usr/local/tmp
cd /usr/local/tmp
wget http://mirrors.163.com/cygwin/x86/release/libmetalink/libmetalink3/libmetalink3-0.1.2-1.tar.bz2
tar -xf libmetalink3-0.1.2-1.tar.bz2
mv /usr/local/tmp/usr/bin/cygmetalink-3.dll /bin
```

好了，再次运行`sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`

估计是因为环境PATH的设置问题，上面这条没有任何回应，好了，死马当活马医，把命令拆了

``` bash
cd ~
wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O .
./install.sh
```

这样有点费劲，把一句拆成了3句，不过成功了，见下图。

![install ohmyzsh](ohmyzsh.jpg)

## 直接打开cygwin进入zsh

直接执行命令 zsh，就会从bash切换到 oh-my-zsh 。

但当我们开启第二个 Cygwin 窗口或者重启 Cygwin 的时候，默认使用的依然是 bash。那怎么样才能默认就使用 zsh 呢？

有两个方法可以做到：

1. 在 `.bashrc` 文件最后添加代码 `exec /bin/zsh`，让 zsh 来替代 bash，就跟刚才我们直接执行 zsh 的操作一样

2. 右键点击 Cygwin 启动图标查看属性，更改其目标为 `c:\cygwin64\bin\mintty.exe /bin/zsh -l`(具体路径要根据你自己的情况)。当然其中的路径需要修改成 Cygwin 的安装路径。

这样在 Windows 下就可以爽快得使用 oh-my-zsh 了。

注意在安装 zsh 之后，cygwin 的 bin 目录下会多一个 mkzsh 脚本。执行此脚本会在 Cygwin 的安装根目录下生成一个 zsh.bat 文件。执行此文件也可以使用带 zsh 登录的命令行界面。只不过……这个脚本开启的是 Windows 的 cmd.exe……比起 Mintty 的界面丑多了……所以还是推荐使用之前说的第二种方法。

## 添加一些别名

因为我喜欢VIM和SUBLIME两个结合着使用，所以我在.zshrc里面添加了subl作为sublime text 3的别名，其实也很简单，但是路径写起来比较费劲，所以这里留个参考

``` conf
alias subl="C:/Program\ Files/Sublime\ Text\ 3/sublime_text.exe"
```

## 一些需要习惯的用法

1. 更强大的tab补全，当你切换目录敲两下tab，他可以列出当前目录下面的所有目录，并且可以使用键盘上下左右键来选择要进入的目录。
2. 更智能的切换目录，比如你要进入一个很深的目录，like this /var/log/nginx/error/lastyear/may/first/monday, 用zsh可以这样输入cd /v/l/n/e/l/m/f/m，然后按tab即可补全整个路径。或者你实现知道当前目录名称，可以直接输入目录，即可进去目录。bash下cd - 可以切换到刚才进入的目录，在zsh下可以记录最近进去过的10个目录，只需要输入d,然后看到对应的0-9的目录，输入对应的数字，即可进入对应的目录（感谢 @JayXon 的提示）。或者直接输入cd -然他后TAB一下，你会发现有最近使用过的31个目录等候你选择。
3. 命令选项补齐，比如输入docker，然后按tab，即可显示出docker都有哪些命令选项。
4. 命令参数补齐，比如要kill一个进程，直接输入kill 进程名，会自动显示出进程的process id，
如果用ssh，则会输出最近用ssh 连接过的主机名，配合.zshrc还可以实现自定义ping命令自动补齐的命令参数。
zstyle ':completion:*:ping:*' hosts 192.168.1.{1,50,51,100,101} www.google.com
5. 大小写字母自动更正，比如我们要cat一下README.txt，直接输入cat readme.txt TAB,之后zsh就会把小写的readme改成大写的。
6. 有着丰富多彩的主题，如果你使用我的脚本安装oh-my-zsh的项目的话，在~/.oh-my-zsh/themes里会找到多达142个主题，看中哪个主题直接在~/.zshrc 里面更改.
7. 更强大的alias命令,比如下面命令，当你在zsh环境下输入hello.py即可直接用vim打开文件编辑，一个tgz的文件即可自动解压缩。

    ``` conf
    alias -s py=vim
    alias -s html=vim
    alias -s tgz='tar zxvf'
    ```

8. 智能命令错误纠正，比如输入apt-gte install somefile,回车后，zsh会提示你是否纠正apt-gte 为apt-get?输入y，后即是正确命令执行。在配合一下zshrc的profile的sudo命令设置，按两下ESC，即可在命令的前面自动加上sudo。

    ``` bash
    ##在命令前插入 sudo {{{
    #定义功能
    sudo-command-line() {
        [[ -z $BUFFER ]] && zle up-history
        [[ $BUFFER != sudo\ * ]] && BUFFER="sudo $BUFFER"
        zle end-of-line #光标移动到行末
    }
    zle -N sudo-command-line
    #定义快捷键为： [Esc] [Esc]
    bindkey "\e\e" sudo-command-line
    #}}}
    ```

9. 最最强大的优点是可以集成各种类型的插件，比如切换目录的可以继承autojump,想跳转到哪里，直接j 加目录名称，真的非常强大，非常便利，这个bash也可以使用。比如想要去nginx目录，可以直接输入j nginx，他会搜索使用率最高的nginx的路径，如果想要去/var/log/下的nginx呢，直接输入j v ng

## 在cygwin上安装hexo

npm就不要纠结是否用apt-cyg安装了，直接去node.js的[官网](https://nodejs.org/en/download/)下载windows 64位版本安装，node.js自带npm，安装后就可以在cygwin下使用npm了。

三句话

``` bash
npm install -g hexo-cli
npm install Hexo-deployer-git --save 
npm install hexo-asset-image --save
```

然后就可以继续写文章了。

## cygwin文件权限的坑坑洼洼

这个问题也是最近才发现的，在cygwin下创建的文件，无法在vscode中打开，甚至用cd命令都进不去建立的文件夹。需要的，给cygwin的运行程序建立快捷方式，然后勾选“使用管理员权限运行程序”，这是第一步。

cygwin将Windows系统的文件权限实现成POSIX的文件权限，而POSIX文件是以ACL来管理文件权限的。POSIX的文件、目录权限是被mount选项来控制的，默认设置为acl。这样做，会导致Windows系统中应用程序的文件权限产生混乱，为了解决这个问题，需要添加noacl参数，将/etc/fatab改为：

``` conf
none /cygdrive cygdrive binary,user,noacl,posix=000
```

但是上面的改动仅仅是修改了`/cygdrive`目录，也就是windows系统中的所有的磁盘（C:、D:、E:、F:等），而cygwin本身的`/`并未修改，同样会存在上述的权限问题，因此需要一并修改，最终完整的`/etc/fatab`文件内容如下：

``` conf
none /cygdrive cygdrive binary,user,noacl,posix=000
C:\cygwin64 / ntfs binary,noacl,override00
C:\cygwin64\bin/usr/bin ntfs binary,noacl,override00
C:\cygwin64\lib /usr/lib ntfs binary,noacl,override00
```

这样设置后，无论是mkdir还是ls或者cd，都可以正常执行了，但是还有一个问题，就是这样建立之后的目录，进入之后，无法启动windows的原生二进制程序，比如笔者通过alias设置的vscode，就无法在新建的目录中启动，但是其他目录是可以的。错误提示如下

``` accesslog
Error: Current working directory has restricted permissions which render it
inaccessible as Win32 working directory.
Can't start native Windows application from here.
```

虽然可以通过在上层目录运行，然后再切换到新建的目录下，毕竟很不方便，经过测试，发现上面这样更改后，mkdir后的文件夹(注意一定要关闭cygwin所有终端之后，重启cygwin才可以)，可以直接运行比如vscode这样的程序，但是之前旧时建立的文件夹则不行，通过查看windows的目录权限，发现新旧两个文件夹主要的区别就是windows账户登录的账户名，是否拥有完全的控制权限。而一旦更改的话，则会遇到`Failed to enumerate objects in the container.`的错误，就是`无法枚举容器中的对象,访问被拒绝`这个错误，网上也没有找到特别好的解决办法，所以我就暂时不想解决了，直接新建了个目录，把文件复制过去了。

``` bash
mv ./dir1/* ./dir2
rmdir ./dir1
mv dir2 dir1
```

这个算是权宜之计吧。因为dir1目录已经无法操作了，应该是之前cygwin留下的后遗症，只能把里面的文件暂时搞到其他地方去了。

如果有哪位同学了解cygwin和windows文件系统权限冲突的解决办法，或者可以稍微解释下这个文件，请尽管赐教，在这里先谢谢了。


## 其他唠叨几句

卸载cygwin的方法，如果你是删掉了整个cygwin64的目录，那么你的个人root主目录也会一起没了，里面的.ssh目录下的文件，也一起没了啊，这个请一定要注意-_-!!!

## 参考文章

1. [Cygwin 上安装 Oh-My-Zsh](http://www.chrisyue.com/install-oh-my-zsh-on-cygwin.html)
2. [mac 装了 oh my zsh 后比用 bash 具体好在哪儿？](https://www.zhihu.com/question/29977255)
3. [cygwin的坑坑洼洼](http://yuanhuan.blog.51cto.com/3367116/1659256)
4. [Windows下安装和使用Git（Cygwin篇）](http://www.worldhello.net/gotgit/01-meet-git/050-install-on-windows-cygwin.html)
