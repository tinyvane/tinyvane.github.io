---
title: wireshark抓包结果分析
date: 2017-03-09 12:41:54
categories: 折腾
tags: [wireshark, 抓包, 斗鱼]
urlname: learn_how_to_use_wireshark
---

## 文章更新

1. 20170218-初次成文

## 为什么会有这篇文章

最近在学习斗鱼如何登陆发弹幕，所以借助wireshark这款工具，可以轻松的获得本机和斗鱼之间的送举报，但是wireshark掌握起来确实不容易，所以先在这里记录一下。

## wireshark的过滤指令

1. ip.src == 13.13.13.13 筛选本地ip地址为13.13.13.13的数据包
2. ip.dst == 12.12.12.12 筛选远程ip地址为13.13.13.13的数据包
3. tcp / http / ssl 筛选不同类型的数据包
4. 允许使用 || && != 等不同的符号来连接条件

## 参考文章

1. []()