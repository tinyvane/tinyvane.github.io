---
title: 更新wordpress过程中遇到“无法创建目录”问题如何解决？
date: 2015-12-07 22:20:19
categeory: 效率
tags: [wordpress, 权限]
urlname: how_to_solve_problem_of_directories_could_not_be_created_when_updating_the_wordpress
---

这个问题其实困扰我好久了，阿里云用的linux一键安装包，因此没注意使用的是什么ftp程序，只知道默认用户名是www，

google了很久，终于找到了一劳永逸的办法

就是

在更新之前

在xshell里面

#chmod -R 777 /alidata/www/wuliaole.com/

更新之后，

#chmod -R 755 /alidata/www/wuliaole.com/

完美