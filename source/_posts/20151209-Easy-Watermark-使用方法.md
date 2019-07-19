---
title: Easy Watermark使用说明
date: 2015-12-09 01:40:54
categories: 折腾
tags: [软件, 水印]
urlname: the_introduction_of_easy_watermark
---

我们不能防止别人抄袭帖子，只能留一点优雅的标记，增加下别人抄袭的成本，但是，从另外一个方面，本来知识是抄不来的，如果你的图片是你的原创，加上个水印，也是顺理成章的事情。这里我介绍我的博客使用的水印程序，[给wordpress图片加水印的3款插件](http://www.lyoyoo.com/index.php/archives/1250)，这是国内一个博主的介绍帖子，无奈我去搜索这几个插件的时候，安装量太少了，因为我是懒人，担心开发者维护一段时间不管了，所以就选了Easy Watermark这款插件，安装量很大了，而且看评论里说已经用了3-4年了，决定，就是他了。

首先是在WORDPRESS的插件市场里，搜索[Easy Watermark](http://wordpress.org/plugins/easy-watermark/)，安装。

然后，激活插件之后，进入设置 **» Easy Watermark **进行配置。

[![QQ截图20151209002617](http://www.wuliaole.com/wp-content/uploads/2015/12/QQ截图20151209002617-300x260.png)](http://www.wuliaole.com/wp-content/uploads/2015/12/QQ截图20151209002617.png)

注意的是，Easy watermark的设置有3个选项卡（Tab），在General选项卡里，第一项 Add watermark when uploading images是必须选的项目，就是说上传图片后，自动添加水印。但是别着急，没有水印图片加什么呢？

所以，需要进入第二个选项卡Image，去添加一张PNG或者GIF的图片（背景透明，不透明的水印，总觉得效果不是特别好）。然后可以选择图片的透明度，位置，以及是否缩放你的水印图片。

回来说第一个General选项卡，在Image Sizes里，默认选了几项应该满足你的要求，但是有一点，如果你希望把文章里的Featured image也加上水印，就要勾选post-thumbnail这一项。

恩，然后还有比较重要的一点，插件可以保存你上传的原始图片，所以那个Backup选项，如果你服务器空间比较富裕，最好也一起勾上。

![Adding an image as a watermark on images in WordPress](http://cdn2.wpbeginner.com/wp-content/uploads/2014/06/watermark-image.png "Adding an image as a watermark on images in WordPress")

If you don’t want to add an image, but would rather add a text as watermark, then you need to click on the Text tab. You can enter the text you want to be displayed as watermark, choose font, font-size, opacity, and positioning of the watermark text.

也许制作一个水印图片对你比较麻烦，或者你压根不想用图片水印，那么就选择文字水印也是一个不错的选择。在第三个选项卡Text里面，你可以设置文字的字体，字号，透明度，以及位置等。

下面要说的就是这个插件比较牛X的地方了，批量去除或者添加水印！

## 如何批量添加水印

#### Adding Watermark to Your Old Images in WordPress

如果你希望给安装插件之前的所有图片批量添加水印图片的话，进入 **媒体** **» Easy Watermark **然后选择 _Add watermark to all image_ 按钮。

提醒，这个批量添加水印的过程不可逆，所以，最好先备份好了整个网站的图片，然后再做这一步的操作。

![Watermark all images on your WordPress site](http://cdn2.wpbeginner.com/wp-content/uploads/2014/06/watermark-all-images.png "Watermark all images on your WordPress site")

## 如何手动添加水印

首先进入 **设置 » Easy Watermark**，确定没有勾选 _Automatically add watermark to images_

&nbsp;

然后进入 **多媒体 » 媒体库** ，记得勾选左上角的图片显示方式，需要用列表的方式，否则是看不到如图上面的单独添加水印的选项的。

[![QQ截图20151209013414](http://www.wuliaole.com/wp-content/uploads/2015/12/QQ截图20151209013414-300x141.png)](http://www.wuliaole.com/wp-content/uploads/2015/12/QQ截图20151209013414.png)

## 参考资料

http://www.wpbeginner.com/plugins/how-to-automatically-add-watermark-to-images-in-wordpress/

&nbsp;