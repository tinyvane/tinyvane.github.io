---
title: MAC PC Linux多平台移动硬盘加密方案
date: 2016-05-06 18:43:34
categories: 折腾
tags: [MAC, PC, Linux, 移动硬盘, bitlocker, hfs, ext4, hfs for Windows, progon, M3 bitlocker loader]
urlname: portable_harddisk_encryption_method_on_mac_pc_and_Linux
---

## 文章更新

1. 20160520-添加了我的解决方法

## 为什么会有这篇文章

本来我一直在Windows下用`NTFS`格式，然后配合使用系统自带的Bitlocker进行加密挺方便的，无奈，这一切在入手第一台MBP之后彻底改变了。MAC下默认的是`HFS+`格式，而Windows下则是`NTFS`格式（这里就暂时不说`FAT`和`FAT32`了，年代太久远了，个人觉得用的人不会太多）。<!-- more -->

## 三个平台下的文件系统格式

### Windows文件系统

Windows操作系统经过这么多年的发展，文件系统一直在不断进化和演变。比如，Windows 98操作系统以及更早的微软操作系统使用的文件系统是FAT(或FAT16)，Windows 2000以后的版本使用`NTFS`文件系统，而Linux系统下的"正统文件"系统则为`Ext2`(Linux second extended file system, Ext2fs)。在默认的情况下，Windows操作系统是不会识别 Linux 的 Ext2 文件系统。

### Linux文件系统

#### Ext2

这里先来说说这个比较陌生的`Ext2`文件系统，其特点是高效稳定。但是，随着Linux系统在关键业务中的应用逐步加深，应用场景大为扩展，Linux文件系统的弱点也逐渐显露：其中系统缺省使用的Ext2文件系统是`非日志文件系统`。这在关键行业的应用是一个致命的弱点。因此，就有了Ext3`日志文件系统`应用。

#### Ext3

`Ext3`文件系统是直接从`Ext2`文件系统发展而来，目前`Ext3`文件系统已经非常稳定可靠。它完全兼容`Ext2`文件系统。用户可以平滑地过渡到一个日志功能健全的文件系统中来。这实际上了也是`Ext3`日志文件系统初始设计的初衷。

#### Ext4 

Linux kernel(内核)自版本 2.6.28 开始正式支持新的文件系统`Ext4`。`Ext4`是`Ext3`的改进版，修改了`Ext3`中部分重要的数据结构，而不仅仅像`Ext3`对`Ext2`那样，只是增加了一个日志功能而已。`Ext4`可以提供更佳的性能和可靠性，还有更为丰富的功能。（具体请参见文末的参考资料）

### MAC文件系统

#### HFS文件系统

`HFS`全称是`分层文件系统`（Hierarchical File System，`HFS`）,是一种由苹果公司开发，并使用在Mac OS上的文件系统。最初被设计用于软盘和硬盘，同时也可以在只读媒体(如CD-ROM)上见到。

`HFS`首次出现在1985年9月17日，作为Macintosh电脑上新的文件系统。`HFS`用于替换只用于早期Mac型号所使用的平面文件系统Macintosh File System（`MFS`）。因为Macintosh电脑所产生的数据，比其它通常的文件系统，如DOS使用的FAT或原始Unix文件系统所允许存储的数据更多。苹果公司开发了一种新式更适用的文件系统，而不是采用现有的规格。例如，`HFS`允许文件名最多有31个字符的长度，支持`metadata`和`双分支`（每个文件的数据和资源支分开存储）文件。

尽管`HFS`像其它大多数文件系统一样被视为苹果电脑系统的专有的格式，但是`HFS`却为大多数最新的操作系统提供了很好的通用解决方法，以访问HFS格式的磁盘数据。

#### HFS+文件系统

在1998年，苹果公司发布了`HFS Plus`(HFS+)，`HFS+`改善了`HFS`对磁盘空间的地址定位效率低下的缺陷，并在后者的基础上加入了其它改进。当前版本的Mac OS仍旧支持`HFS`，但从Mac OS X开始，`HFS`卷不能作为启动卷用。

`HFS Plus`，或`HFS+`是苹果公司为替代他们的`HFS`而开发的文件系统。它被用在Macintosh电脑（或者其他运行Mac OS的电脑）上。它也是iPod上使用的其中一种格式。`HFS+`也被称为OS X Extended（或误称为`HFS Extended`）。在开发过程中，苹果公司也把这个文件系统的代号命名为`Sequoia`。

`HFS+`是对`HFS`系统的改进，前者支持更大的文件，并用Unicode来命名文件或文件夹，代替了Mac OS Roman或其他一些字符集。和`HFS`一样，`HFS+`也使用`B树`来存储大部分分卷元数据。

#### HFSX文件系统

如果是区别，那么可以认为`HFS+`为是MacOS X系统使用，而`HFSX`是为苹果的移动设备而使用。两者的区别在于`HFSX`文件系统支持大小写，`HFS+`不支持。

> 在维基百科上，我没有找到`HFSX`的词条，看来可能区别并没有大到开一个词条的地步。

另外，MAC OS X下格式化磁盘分区有三种分区表的选择，分别是`GUID`，`APM`和`MBR`。

另外在苹果系统中，`HFS+`的叫法是`Mac OS Extended (Journaled)`， `HFSX`的叫法是`MAC OS Extended (Case-sensitive, Journaled)` 

#### 三种分区表的区别

* `GUID`分区表：使用启动基于Intel的MAC，或将磁盘当做非启动盘用于任何装有MAC OS X V10.4 或更高版本的MAC。

* `APM`：使用该磁盘启动基于PowerPC的MAC 电脑，或将该磁盘当做非启动盘用于任何MAC电脑。

* `MBR`：使用该磁盘启动DOS和Windows电脑，或者将磁盘配合需要DOS兼容分区或Windows兼容分区的设备来使用。

### Windows文件系统

既然上面提到了MAC OS下可以使用MBR分区表，那么接下来，就要说说Windows的文件系统和兼容性的问题了。（未完待续）

## 加密方式的选择

MAC下可以使用FILEVAULT来做整盘加密，Windows 7或者后续版本下当然是使用Windows原生的BitLocker加密功能了。至于跨平台的数据加密方案，在写这篇文章之前，我查阅的资料中提到的比较有名的跨平台方案是TrueCrypt，但是后来这款软件在官方网站上曝出漏洞，劝大家不要再使用TRUECRYPT进行数据加密(PS：有人说是NAS美国国安局掌握这种数据加密方式的漏洞，因此数据加密几乎没有意义，抿嘴笑:D)。

## 我的加密方法

如果你的资料相对有一些私密性，或者有商业性质的资料在保存在移动硬盘中，并且工作环境又跨越Windows平台、MAC平台和Linux平台，那么我推荐你将移动硬盘至少划分为三个分区：一个分区使用MAC的FILEVAULT进行数据加密，第二个分区使用BIT LOCKER进行数据加密，另外一个分区，则被用于在PC和MAC以及Linux系统下复制、交换文件资料，这个分区划分至少100G(个人建议而已)，并且使用`exFAT`格式进行格式化

> MAC和PC同时识别的文件系统主要有ExFAT和FAT32，但是FAT32格式不支持单个文件大于4G。然而，网上有人消息说ExFAT格式在PC和MAC跨平台数据交换中存在数据丢失的问题，这个我后面会提到。

### 如何实现

我的硬盘主要在Windows 10以及OSX系统下使用，为了照顾MAC的兼容性，移动硬盘在MAC下进行分区。

### 使用MAC下的“磁盘工具”

如下图所示，使用CONTROL+SPACE 搜索 磁盘工具即可。

![磁盘工具](disktool.png)

我这里的截图，已经和初次连接移动硬盘有所不同了，先大概说一下流程，我是1T的外置移动硬盘，分了3个区，需要注意的是，在分区的时候，一定要点开“分区布局”下面的“选项”按钮，在这里选择GUID分区表方式，因为如果选择了MBR方式的话，这个磁盘在Windows 10下好像是无法识别出来的（有点记不清楚了，下次如果有机会重新分区，再来更新这个地方，不好意思了各位）。

选择好了GUID分区表，再来说一下，3个分区每一个具体如何选择格式。

### 三个分区格式的具体选择 

第一个分区给WINDWOS 10使用，因为需要让WINDWOS可以识别这个分区，我选择了MS-FAT格式来格式化，你也可以选择EXFAT，这种格式在WINDWOS 10也可以识别。

这里顺便就把后面的两个分区格式一起说了，第二分区给MAC使用，所以采用了HFS格式，我没有使用区分大小写，这种方式如果你看了前面的介绍，是给APPLE的移动系统使用的，具体我也没有深入研究。

第三个分区，我使用了EXFAT分区格式，这个格式虽然网上说的存在丢失数据的问题，但是因为本身就属于交换数据的分区，文件拷贝到这个盘上，在很短时间内就会归档到其他电脑上，并且这个格式支持文件大小超过4G，还是比较理想的，并且最重要的是，第三个分区作为PC和MAC交换数据使用，需要两个系统都能识别。

### 数据如何加密

分区之后，把移动移动插到Windows 10系统下，因为我没有使用HFS+ FOR WINDWOS 10那个软件，所以系统默认只能识别到第一个，和第三个分区，第一个分区，我重新使用NTFS进行了格式化，并且开启了BITLOCKER。这样PC下的数据安全，暂时得到了保障。

然后，把移动硬盘插回MAC系统下，系统只能识别第二个和第三个分区，然后，在桌面上右键第二个分区，选择启动FILEVAULT，这样MAC下的数据安全暂时得到了保证。

有朋友会说了，如果在Windows系统下安装Paragon HFS+ for Windows® 10的话，也可以识别HFS+分区不是么？其实我之前有意购买那个软件的，单个正版序列号，在淘宝上也就是65元左右，但是后来考虑到使用BITLOCKER和FILEVAULT，就没啥必要了，因为这俩加密方式，并不能通过第三方软件进行存储（有一个M3软件，支持在MAC系统下“读取”BITLOCKER加密的数据，但是仅仅限于读取，无法写入，所以对我来说，意义不大，而且还可以省下一笔银子，并且更重要的是，感觉自己都会乱的）。因此，BitLocker加密，和FileVault加密工作，仿佛分别变成了WINDWOS和OSX系统各自的“福利”。

### 最后的最后

其实，这个跨平台的加密方法，目前用着还是相对安全，至少数据在各自不同的分区下，都使用了可靠的加密方式，代价就是过程稍微复杂，并且会浪费一些时间。

我个人因为养成了在不同系统之间及时备份数据的习惯，因此移动硬盘上第三个分区上的数据，并不会长时间（不超过24小时）保存。我个人有整理数据的习惯，很快就会把三个分区上的数据尽快转义到各自的文件系统中去。因此，安全性就是最重要的，所以本文中没有使用第三方加密工具，而是分别使用了各自平台上最为成熟和稳妥的加密方式。

好了，这个话题终于告一段落了，折腾了我3个礼拜，终于可以暂时收笔了。

因为比较懒，文章中有一些概念还没有及时学习清楚，先这样吧。

## 参考资料

1. [Ext2、Ext3和Ext4之间的区别](http://misujun.blog.51cto.com/2595192/883949)
2. [什么是文件系统？](https://www.ibm.com/developerworks/cn/Linux/l-Linux-filesystem/)
3. [分层文件系统HFS](https://zh.wikipedia.org/wiki/%E5%88%86%E5%B1%82%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F)
4. [HFS+介绍](https://zh.wikipedia.org/wiki/HFS%2B)
5. [HFSX和HFX+的区别](http://blog.csdn.net/hgzty/article/details/10150289)
