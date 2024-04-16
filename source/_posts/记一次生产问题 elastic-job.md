---
title: 记一次生产问题 elastic-job
tags: [Linux, 运维]
date:  2019/08/28 17:02
categories: Linux教程
---

#### 问题截图
![Mou_icon](http://blog.lutao1726.top/elastic-job-01.jpg)
```less
com.dangdang.ddframe.job.exception.JobConfigurationException: Job conflict with register center. The job 'UserLongTimeNoLoginTask' in register center's class is 'com.zhjs.saas.job.task.SupplyCreditNoLoginTask', your job class is 'com.zhjs.saas.job.task.UserLongTimeNoLoginTask'
        at com.dangdang.ddframe.job.lite.internal.config.ConfigurationService.checkConflictJob(ConfigurationService.java:79)
        at com.dangdang.ddframe.job.lite.internal.config.ConfigurationService.persist(ConfigurationService.java:70)
        at com.dangdang.ddframe.job.lite.internal.schedule.SchedulerFacade.updateJobConfiguration(SchedulerFacade.java:103)
        at com.dangdang.ddframe.job.lite.api.JobScheduler.init(JobScheduler.java:105)
        at com.zhjs.saas.job.manager.JobManager.lambda$init$0(JobManager.java:51)
        at java.util.LinkedHashMap$LinkedValues.forEach(LinkedHashMap.java:608)
        at com.zhjs.saas.job.manager.JobManager.init(JobManager.java:42)
        at com.zhjs.saas.job.manager.JobManager$$FastClassBySpringCGLIB$$31b26d06.invoke(<generated>)
        at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:204)
        at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.invokeJoinpoint(CglibAopProxy.java:736)
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:157)
        at org.springframework.aop.interceptor.AsyncExecutionInterceptor$1.call(AsyncExecutionInterceptor.java:115)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

```
>引起：com.dangdang.ddframe.job.exception.JobConfigurationException：作业与注册中心冲突。 注册中心班级的工作’UserLongTimeNoLoginTask’是’com.zhjs.saas.job.task.SupplyCreditNoLoginTask’，你的工作类是’com.zhjs.saas.job.task.UserLongTimeNoLoginTask’

#### 解决问题

###### 下载 [ZooInspector.zip](https://issues.apache.org/jira/secure/attachment/12436620/ZooInspector.zip)
###### 解压，进入目录ZooInspector\build，
 运行zookeeper-dev-ZooInspector.jar
```bash
java -jar zookeeper-dev-ZooInspector.jar  //执行成功后,会弹出java ui client
```
![Mou_icon](http://blog.lutao1726.top/elastic-job-02.jpg)

###### 删除节点
![Mou_icon](http://blog.lutao1726.top/elastic-job-03.jpg)

选中节点后点删除图标
![Mou_icon](http://blog.lutao1726.top/elastic-job-04.jpg)

不删除节点，手动改配置也是可以的：
![Mou_icon](http://blog.lutao1726.top/elastic-job-05.jpg)