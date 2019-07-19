---
title: 如何降级Resilio Sync
categories: 折腾
tags: [Resilio Sync]
date: 2017-05-19 19:16:12
urlname: how_to_downgrade_resilio_sync
---

## 文章更新
1. 20170519-初次成文
2. 20170727-更新文章初衷

## 为什么会有这篇文章

Resilio Sync曾经确实是个非常好用分享工具，无奈这种p2p的方式并不能得到有限监管，所以他的tracker server某个时间节点之后，被盯上了，具体是谁不方便表达，大家自己脑补吧。后来从2.5.4开始，就频繁出来国内多个节点无法互相看到的问题，当时以为降级可以解决问题，后来才觉得还是太天真了。

## 如何降级Resilio Sync

在Resilio Sync的官方网站找到最新版本发行的说明。

https://forum.resilio.com/topic/43414-latest-desktop-build-252/

DOWNGRADE
Resilio Sync will make a backup copy of all your settings prior migration into "sync-v2.3.7-1471003319.backup"-like folder (version and timestamp may vary). If you decide to return to 2.3.*, you'll need to manually copy the content to your  storage folder, remove Resilio Sync and be aware of next downgrade drawbacks:

You'll downgrade to "Free" version and you'll have to re-apply license manually.  File association with license files *.btskey is broken so you'll need to apply license in BTSync UI
Linking new devices to downgraded Sync is not possible. You'll need to recreate identity if you want to link some new device.
Downgrade is not supported for Selective Sync folders  (as all placeholder files are renamed to *.rsl*).  If you downgrade and your Sync has Selective Sync folders, your files are going to be deleted on all peers.

好了，我翻译一下

1，去Resilio Sync的安装目录下

找到sync-v2.3.7-xxxxx.backup这样一个目录，我的见下图

![](128720C3-EA91-4191-B7E3-0EFE12321A61.png)

看看这个目录下面都有什么东西呢？

![](AD23F605-E23E-4D3B-AB69-3D21DEBBB27B.png)

你需要手动把这个目录下的这些文件，保存到你的“storage folder”，存储文件夹，这个目录在哪里呢？其实就是在Sync UI里面设置里，有一个设置，见下图

![](F7C3AF3A-51A5-47D5-A0B4-7CC4C97D1D31.png)

上面这个界面有点奇怪，因为它是新版Sync的，2.5.2，是我想卸载掉的版本。

但是当我去这个位置下的时候，完全没有所谓的settings.dat这些文件呢。只好求助于Windows的搜索功能了，

好了，找到了,见下图

![](35AA9DAB-3A6D-4923-A100-D10AB05A5E91.png)

看来，其实所谓的存储目录，就是 `Users` 目录下的用户名目录下的 `AppData\Roaming\Resilio Sync` 目录？

从下面来个对比

![](9744B64F-EF24-4B21-9F0A-70B2914FAA0C.png)

嗯，看来没错了，左侧是目前在用的目录，右侧是backup文件下的文件。

好了，现在我就开始卸载新版的Sync软件，然后再安装老版本软件，然后再覆盖就应该OK了。

## 停止Sync

这个我就不说了，直接右键退出Sync就可以了。

## 卸载

![](4E334020-0F84-4F50-A582-40C54A3FA3AC.png)

我没有勾选 Remove settings 的选项，这个设置是Sync方便你下次还想装的话，就直接用了目录下的配置文件的目的。

## 安装老版本

卸载后，发现其实那个Resilio Sync目录下并没有少多少东西，甚至Sync的主应用程序都在。不管了，继续装。

## 覆盖配置文件

神奇的事情发生了，我甚至都没有覆盖配置文件，老版本的Sync就自动运行起来了，而且也连上了新版Sync的peer，让我惊喜。

虽然这样也算是安装好了，但是还是不放心，所以还是推出了老版本的Sync，把backup的文件重新覆盖了。

然后，又可以连接上家里电脑的peer了，感觉还是比较满意，正好截个图，留下个配置的说明。

![](1A59B504-0D99-4815-8FFB-AA980799F775.png)

## 成功

算是小成功咯。

## 参考资料

1. [Latest Desktop Build 2.5.2](https://forum.resilio.com/topic/43414-latest-desktop-build-252/)