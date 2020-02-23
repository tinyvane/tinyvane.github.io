---
title: LaunchAgents与LaunchDaemon
date: 2017-02-19 01:04:08
categories: MacOS
tags: [mac, launchagents, launchdaemon, osx]
urlname: launchagents_and_launchdaemon_on_mac_osx
---

## 文章更新

1. 20170218-初次成文

## 为什么会有这篇文章

在MAC OSX上使用Homebrew安装过程中，多次涉及到launchctl的使用，每个服务存放的位置不同，有的放在了Library/LaunchDaemon下，有的放在了Library/LaunchAgents下，所以有点困惑，就想着把区别弄明白。所以做了一些研究。

## 什么是 launchd ?

Mac OS X 从 10.4 开始，採用 launchd 来管理整个系统的服务和进程。传统的 UNIX 系统会使用 `/etc/rc.*` 或其他的机制来管理开机时要启动的`启动服务`，而从Mac OS X 10.4开始使用 launchd 来管理，它的`启动服务`，分别叫做 `Launch Daemon` 和 `Launch Agents`。而视为服务程序，一般应该是后台进程，一般不提供GUI，也不应该跳到控制台的前台来(当然有些例外)。

launchd 管理的后台进程主要有四种，分别是：

1. Launch Daemon: 在开机时载入 (load) 。
2. Launch Agent: 在使用者登入时载入。
3. XPC Service: 好像是 10.7 才有的，我还没灌 10.7 ，先跳过。
4. Login Items: 在 User 登入时执行。有两种方法可以用程式新增项目到 Login Item：
  1. Shared File List：会出现在 Account 偏好设定的 Login Item 清单。
  2. Service Management Framework：这个就不会出现在 Login Item 清单。

（以下把重点放在 Launch Daemon / Agent 。至于 XPC 和 Login Item 就留待其他比较在行的大大来解释。）

## Launch Daemon & Launch Agent

Launch Daemon 和 Launch Agent 是同一种东西在不同范围下的异名。Launch Daemon 是系统级别的服务 ，称为 daemon，Launch Agent 是用户级别的服务，称为 Agent，前者在开机时会载入（load），后者在使用者登入时（才）会载入。

如果你打开`活动监视器`，并在`显示`选项中，切换到`所有进程，分层显示` ，你会发现有个 kernel_task 会在最上层，下层紧跟着 launchd ，它下面有很多子进程的拥有者都是 root 。很多时候，还会有一个相同级别的 launchd ，启动用户正是你自己，它底下的子进程的用户也几乎都是你自己。前者所包含的这些子进程，由 root 执行的称为 Launch Daemons ，后者由使用者(你)执行的称为 Launch Agents 。

两个 launchd 分别由各自的 plist文件(就是在 LaunchDaemon 或 LaunchAgents 目录中看到的 *.plist 文件)所管理，plist文件是 XML 格式。

## launchd 服务进程的生命周期

由 launchd 所管理的服务（Launch Daemon 、 Launch Agent）是要先由 launchd 载入（load）以后才会执行（run），但载入之后并不一定马上执行。在苹果的官方文件说明了 `OSX内核` 载入完成后会发生的事，用来说明 Launch Daemons 、Launch Agents 及其各自子进程的的生命週期。

开机时，会先载入 OS Kernel ，载入完成后就执行 launchd ，用来载入系统级别的子进程（daemons）。这个系统级别的 launchd 在开机时会做这些事：

1. 载入 (load) 存放在这些目录下的 plist ：
  * /System/Library/LaunchDaemons
  * /Library/LaunchDaemons
2. 注册那些 plist 文件内设定的 sockets (port) 和 file descriptors
3. 执行 (run) KeepAlive = true 的 daemons ，当然 RunAtLoad = true 的也会启动。

该运行的进程运行之后，就出现了登录窗口，提示使用者登入。有设定自动登入的话，就会跳过这项。

在使用者登入以后，会执行属于该使用者的 launchd ，负责处理 Launch Agent ，做的事跟上面载入 Launch Daemon 很像，差别在于它从以下的目录载入 plist：

1. /System/Library/LaunchAgents
2. /Library/LaunchAgents
3. ~/Library/LaunchAgents

由使用者执行的任何程式也都是 launchd 来执行的，所以 launchd 也是该使用者的所有子进程的母进程。

在使用者登出、关机或重新开机时，会触发`终止事件`，用来接受使用者的登出、关机、重新开机等指令的进程是 loginwindow 。它会先向使用者确认，一但确认，就会对每个由该使用者的 launchd 所启动的 processes 送出 `终止信号`，如果是 Cocoa 则送出 Cocoa API 的 event，其他的就送出 SIGTERM 要他们自我了断，45 秒之后，除了 Cocoa 的应用程式可以丢出某个 error 来取消这整个 termination process，其他还没结束的都会被 kill 掉。

这就是为什麽 loginwindow 这个 process 会一直存在，它要负责把该使用者执行起来的 processes 统统清掉。而 per-user services 都关掉以后，就回到 loginwindow ，或是执行关机、重新开机的流程，后两者就是照着差不多的流程去关掉所有 system-wide services 。

## 参考文章

1. [Mac OS X 的 Launch Daemon / Agent](https://blog.yorkxin.org/2011/08/04/osx-launch-daemon-agent)