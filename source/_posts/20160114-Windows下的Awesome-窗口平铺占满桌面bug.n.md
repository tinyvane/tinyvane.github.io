---
title: Windows下的awesome让所有窗口平铺沾满桌面的工具bug.n
date: 2016-01-14 11:11:21
category: 效率
tags: [平铺, 桌面, 窗口]
urlname: how_to_tile_all_windows_on_windows_10_pc
---

## 更新日志

1. 20160114-初次成文
2. 20170315-添加内容，放弃了bug.n

## 为什么会有这篇文章

总有一些东西是你自己不知道是否需要，但是在看到的那一刹那，相见恨晚。

2017年3月15日，放弃了bug.n，全面使用Windows 10的自带快捷键。请移步这里，看我的另一篇文章，[http://www.wuliaole.com/post/how_to_use_windows_without_mouse](如何不用鼠标使用WINDOWS 10)。

## 介绍

因为曾经在知乎上，看到玩多屏的人介绍过 Awesome，就一直寻找 Windows 平台下的类似软件，结果找到了这个名叫bug.n的小软件（名字是不是很拗口？）。

软件在GITHUB上的[地址](https://github.com/fuhsjr00/bug.n)

先看一下我自己开启bug.n后桌面的窗口布局：

![My bug.n desktop layout](img_569711fb30fc5.png)

值得一提的是，窗口顶端还有一个banner，显示的信息包括：

* 虚拟桌面列表
* 当前虚拟桌面使用什么布局
* 活动窗口的标题
* 日期时间

bug.n支持虚拟桌面。Windows系统只有一个桌面，但通过bug.n可以虚拟出很多桌面。如果你正在一个桌面上玩游戏或看碟，看到老板来了，你可以迅速切换到早就准备好的工作桌面。老板在任务栏上不会发现任何你娱乐过的蛛丝马迹。

通过按Win+ Shift + 加数字n，可以切换到第n个桌面。用鼠标直接点击banner上的虚拟桌面按钮也可以。另外用鼠标右键点击某个虚拟桌面，会把当前的活动窗口送到那个虚拟桌面去。

每个桌面支持三种布局模式：

* 平铺模式(tiling): 所有窗口平铺，左边是主窗口，右边是窗口队列。按 Win+ Shift + t 可以切换到tiling模式。
* 浮动模式(floating): 所有窗口浮动，可以互相遮盖，就是我们平常用的模式。按 win+ Shift + f可以切换到floating模式。
* 全屏模式(monocle): 所有窗口最大化，一次只显示一个。按 Win+ Shift + m 可以切换到 monocle 模式。
用鼠标右键点击banner上的布局按钮可以在这三种布局间切换。

通过窗口键加方向键可以调整主窗口大小和改变窗口队列，自己试试就知道。如果你觉得受不了了，按 Win + Shift +  Ctrl + Shift +  q，可以退出bug.n。

我在Win10上用bug.n，自己的配置写在 `C:\Users\Administrator\AppData\Roaming\bug.n\Config.ini` 中。

重点介绍一些常用的快捷键

`Win + Shift + 左右方向键`，是调整分割比例，这个很好用，左侧可以调整为正好一个网页的宽度。

`Win + Shift + 上下`，可以调整活动窗口。

`Win + Shift + Shift+ Shift + 上下键`，可以让活动窗口在不同位置之间跳转。

`Win + Shift + Shift+ Shift + Enter`，让当前窗口和左侧活动主窗口对调位置很方便。

`Win + Shift + Ctrl+ Shift + q`，退出bug.n。

`Win + Shift + c`，关闭当前窗口。

下面是官方文档中的推荐用法

1. `Win + Down` ::View_activateWindow(0, + Shift + 1)
    Activate the next window in the active view.

2. `Win + Up`::View_activateWindow(0, -1)
    Activate the previous window in the active view.

3. `Win + Shift + Down`::View_shuffleWindow(0, + Shift + 1)
    Move the active window to the next position in the window list of the view.

4. `Win + Shift + Up`::View_shuffleWindow(0, -1)
    Move the active window to the previous position in the window list of the view.

5. `Win + Shift + Enter`::View_shuffleWindow(1)
    Move the active window to the first position in the window list of the view.

    You may also move the active window to any other absolute position in the window list by using the first parameter.

6. `Win + c`::Manager_closeWindow()
    Close the active window.

7. `Win + Shift + d`::Window_toggleDecor()
    Show / Hide the title bar of the active window.

8. `Win + Shift + f`::View_toggleFloatingWindow()
    Toggle the floating status of the active window.

    The floating status effects the tiling of the active window (i. e. dis- / regard it).

9. `Win + Ctrl + m`::Manager_minimizeWindow()
    Minimize the active window.

    This implicitly sets the window to be floating.

10. `Win + Shift + m`::Manager_moveWindow()
    Move the active window by key.

    This implicitly sets the window to be floating.

11. `Win + Shift + s`::Manager_sizeWindow()
    Resize the active window by key.

    This implicitly sets the window to be floating.

12. `Win + Shift + x`::Manager_maximizeWindow()
    Move and resize the active window to the size of the work area.

    This implicitly sets the window to be floating.

13. `Win + i`::Manager_getWindowInfo()
    Get information for the active window.

    The information being id, title, class, process name, style, geometry, tags and floating state.

14. `Win + Shift + i`::Manager_getWindowList()
    Get a window list for the active view.

    The list contains information about the window id, title and class.

15. `Alt + Down`::View_moveWindow(0, + Shift + 1)
    Manually move the active window to the next area in the layout.

    This has only an effect, if dynamic tiling is disabled (Config_dynamicTiling=0).

16. `Alt + Up`::View_moveWindow(0, -1)
    Manually move the active window to the previous area in the layout.

    This has only an effect, if dynamic tiling is disabled (Config_dynamicTiling=0).

17. `Alt + Shift + Enter`::Manager_maximizeWindow()
    Move and resize the active window to the size of the work area.

    This implicitly sets the window to be floating.

18. `Alt + <n>`::View_moveWindow(<n>)
    Manually move the active window to the nth area in the layout.

配合Chrome的Vimium，可以提高不少效率。

目前我正在研究的是Config.ini的一条规则，

`Config_rule=WebChat*;.*;;1;0;0;0;0;0;`

就是比如像微信电脑版这样的程序，其窗口的最小是一个固定值，如果让他按照普通窗口那样排列，基本什么也看不到，目前代替的方法就是Win+ Shift + c，然后看到新信息，Ctrl+ Shift + Alt+ Shift + w取消息，回复完了，再关闭微信（注：Ctrl+ Shift + Alt+ Shift + w是微信默认快捷键，非bug.n）。

## 参考资料

1. [平铺式窗口管理器](http://www.cnblogs.com/jiqingwu/p/4311202.html)
2. [Official bug.n manual](https://github.com/fuhsjr00/bug.n/tree/master/doc)