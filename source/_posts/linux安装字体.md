---
title: linux安装字体
tags: [Linux, 运维, Java]
categories: Linux教程
date:  2019/04/18 14:02
---
<!-- more -->

linux安装字体
=======================
安装fontconfig
> yum -y install fontconfig

将windows字库导入到linux  /usr/share/fonts/windows目录下
> cp -rf windows /usr/share/fonts/

更改这些字体库的权限：
>  chmod 755 /usr/share/fonts/windows/* 

然后进入Linux字体库：
> cd /usr/share/fonts/windows/   

接着根据当前目录下的字体建立scale文件
(没有要安装 
```bash
 yum install mkfontscale 
```
)
> mkfontscale    

接着建立dir文件
> mkfontdir    

运行
> fc-cache  

 