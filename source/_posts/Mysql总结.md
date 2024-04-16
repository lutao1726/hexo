---
title: Mysql总结
date: 2019-01-18 09:00:32
categories: 框架
tags: [Mysql, Java]
---
![Mou_icon](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.todavianose.com%2Fwp-content%2Fgallery%2Fgaleria%2F423.jpg&refer=http%3A%2F%2Fwww.todavianose.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1623575405&t=f33dc415315194d090e8975f8c942c29)
<!-- more -->
### 数据库三范式
###### 第一范式
> 列的原子性
###### 第二范式
> 每列与主键有关系
###### 第三范式
> 所有列与主键有直接关系，而非间接关系
#### 反范式
> 冗余数据，从而增加减少关联

### 常用搜索引擎
###### InnoDB

###### MYISAM

#### 使用场景

#### 常用语句
```sql
--查看引擎
show engines;
--设置表使用什么引擎
alter table t_user engine = 'InnoDB';
```


### 索引
#### 为什么要使用索引
* 避免全表扫描，提升查询效率
* 主键，普通键，唯一键都可以作为索引

#### 索引分类
###### 聚簇索引
###### 非聚簇索引
###### 联合索引
###### 覆盖索引
###### 主键索引
#### 数据结构
#### 创建原则
#### 索引失效


### 事务与锁
#### 事务
#### 常见问题
#### 隔离级别
#### 锁

### 调优
#### 优化方式
#### 分析技巧

### 执行过程

### 日志

### 分库分表

### 读写分离

### 常见面试题











