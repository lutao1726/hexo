---
title: Java基础回顾(1)-开篇
tags: [Java, Java基础]
categories: Java教程
date:  2017/9/16 17:29
---
<!-- more -->

Java简介
=================
Java是由Sun Microsystems公司于1995年5月推出的Java面向对象程序设计语言和Java平台的总称。由James Gosling和同事们共同研发，并在1995年正式推出。
Java分为三个体系：
+ JavaSE（J2SE）（Java2 Platform Standard Edition，java平台标准版）
+ JavaEE(J2EE)(Java 2 Platform,Enterprise Edition，java平台企业版)
+ JavaME(J2ME)(Java 2 Platform Micro Edition，java平台微型版)。

主要特性
=================
+ 面向对象
+ 简单性
+ 分布式
+ 健壮性
+ 安全性
+ 可移植性
发展历史
=================
+ 1995年5月23日，Java语言诞生
+ 1996年1月，第一个JDK-JDK1.0诞生
+ 1997年2月18日，JDK1.1发布
+ 1998年12月8日，JAVA2企业平台J2EE发布
+ 1999年6月，SUN公司发布Java的三个版本：标准版（JavaSE,以前是J2SE）、企业版（JavaEE以前是J2EE）和微型版（JavaME，以前是J2ME）
+ 2006年12月，SUN公司发布JRE6.0
+ 2009年04月20日，甲骨文74亿美元收购Sun。取得java的版权。
+ 2011年7月28日，甲骨文发布java7.0的正式版。
+ 2014年3月18日，Oracle公司发表Java SE 8。

Java开发工具
================
+ Java JDK
+ 编辑器：notepad 等文本编辑器
+ IDE： eclipse，idea 等

Java开发环境配置
================

下载JDK
-----------------
下载地址：http://www.oracle.com/technetwork/java/javase/downloads/index.html
配置环境变量
-----------------
1.安装完成后，右击"我的电脑"，点击"属性"，选择"高级系统设置"；
2.选择"高级"选项卡，点击"环境变量"；
在"系统变量"中设置3项属性，JAVA_HOME,PATH,CLASSPATH(大小写无所谓),若已存在则点击"编辑"，不存在则点击"新建"。
变量设置参数如下：
+ JAVA_HOME    变量值：C:\Program Files (x86)\Java\jdk1.8.0_91        // 要根据自己的实际路径配置
+ CLASSPATH    变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;         //记得前面有个".".;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;         //记得前面有个"."
+ Path  变量值：%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;

> path环境变量的作用:保证javac命令可以在任意目录下运行。  
> classpath环境变量的作用:保证class文件可以在任意目录下运行。


测试环境配置是否成功：打开cmd 输入java ，javac ，java -verson 等都能判断是否配置成功

JDK,JRE,JVM的作用及关系
-----------------------
  > + 作用  
		JVM：保证Java语言跨平台   
		JRE：Java程序的运行环境   
		JDK：Java程序的开发环境   
  > + 关系  
		JDK：JRE+工具  
		JRE：JVM+类库

第一个Java程序
-----------------------    
``` java
  class HelloWorld{
    public static void main(String[] args){
      System.out.println("hello world!");
    }
  }
```
程序解释：
+ Java程序最基本的单位是类
