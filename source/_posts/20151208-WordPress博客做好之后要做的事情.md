---
title: wordpress博客做好之后要做的事情
date: 2015-12-08 21:58:04
categories: 折腾
tags: [wordpress, 博客]
urlname: what_need_to_be_done_after_installation_of_wordpress
---

## 安装

注意调整xshell里的权限，比如www和ftp账号的

### 调整.htaccess文件

默认centos下是没有这个文件的

### 安装theme

我目前用的d8的，记得要保证theme本身不带什么插件，否则就和自己后来装的容易冲突了，同时换一下logo

### 安装插件

删掉hello dolly，启动akimest，api填进去就ok了，安装了高级版本的TinyMCE编辑器，目前除了好用点，没发现其他用处。

不用多说或者畅言，评论在站里还是比较好，因为wordpress 4.3.1默认带了plupload，所以不需要安装之前的那个`drag and drop`插件了

插一句，关于自动更新的，我在另外一个帖子里写了解决办法，[更新wordpress过程中遇到“无法创建目录”问题如何解决？](http://www.wuliaole.com/post/how_to_solve_problem_of_directories_could_not_be_created_when_updating_the_wordpress)

总之就是`chmod -777`或者`chmod -755`，不多说了，会了就很方便。

### 水印问题

我用的Easy Watermark 0.6.0，这里有一个帖子，介绍了不少水印的插件，但是吧。。。可能作者推荐的，我这里显示安装量和活跃度比较低，就用了一个2个月前更新且符合4.3.1的水印工具，大家自己选择吧，用着顺手就好。

[给wordpress图片加水印的3款插件](http://www.lyoyoo.com/index.php/archives/1250)。我自己写了一个中文版的使用介绍。

### 防盗链

### 备份

### 恢复

## 参考资料

http://www.zhihu.com/question/22864602