---
title: JVM调优
date:  2022/11/15 16:41
categories: 面试系列
tags: [Java,Interview]
---
<!-- 展示图片 -->
![Mou_icon](https://img2.baidu.com/it/u=1734247813,400897296&fm=253&fmt=auto&app=138&f=PNG?w=1111&h=500)
# JVM调优

## 1. JVM调优的步骤

一般情况下，JVM调优可通过以下步骤进行：

- 分析GC日志及dump文件，判断是否需要优化，确定瓶颈问题点；
- 确定JVM调优量化目标；
- 确定JVM调优参数（根据历史JVM参数来调整）；
- 依次调优内存、延迟、吞吐量等指标；
- 对比观察调优前后的差异。

## 2. JVM调优的参数

JVM调优的参数有很多，这里列出一些常用的参数：

- -Xms：JVM启动时初始堆大小；
- -Xmx：JVM堆最大值；
- -Xmn：年轻代大小；
- -XX:PermSize：永久代初始大小；
- -XX:MaxPermSize：永久代最大值；
- -XX:SurvivorRatio：年轻代中Eden区域与Survivor区域的比例；
- -XX:NewRatio：老年代与年轻代的比例；
- -XX:MaxTenuringThreshold：垃圾最大年龄；
- -XX:+UseConcMarkSweepGC：使用CMS垃圾回收器；
- -XX:+UseParNewGC：使用ParNew垃圾回收器；
- -XX:+UseParallelGC：使用Parallel垃圾回收器；
- -XX:+UseSerialGC：使用Serial垃圾回收器；
- -XX:+UseG1GC：使用G1垃圾回收器。

## 3. JVM调优的工具

JVM调优的工具有很多，这里列出一些常用的工具：

- jstat：用于监视JVM统计信息的命令行工具；
- jmap：用于生成JVM堆转储快照的命令行工具；
- jstack：用于生成JVM线程转储快照的命令行工具；
- jconsole：用于监视JVM的GUI工具；
- VisualVM：用于监视JVM的GUI工具。

## 4. JVM调优的实例

以下是一些JVM调优的实例：

- [JVM调优汇总（JDK1.8）](https://blog.csdn.net/qq_36080515/article/details/120502104)
- [面试官：如何进行 JVM 调优（附真实案例）](https://zhuanlan.zhihu.com/p/488615913)
- [JVM性能调优详解](https://zhuanlan.zhihu.com/p/91223656)
- [JVM调优详解（一次java性能优化实战）](https://juejin.cn/post/6958097237398257671)
