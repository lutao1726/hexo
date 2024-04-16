---
title: Spring总结
date: 2019-01-18 09:00:32
categories: 框架
tags: [Spring, Java]
password: lt123456
---
![Mou_icon](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3833687770,1236182247&fm=26&gp=0.jpg)
<!-- more -->
Spring 概述
-------------------
>Spring 是个java企业级应用的开源开发框架。Spring主要用来开发Java应用，但是有些扩展是针对构建J2EE平台的web应用。Spring 框架目标是简化Java企业级应用开发，并通过POJO为基础的编程模型促进良好的编程习惯。

![Mou_icon](https://gss0.bdstatic.com/-4o3dSag_xI4khGkpoWK1HF6hhy/baike/w%3D268%3Bg%3D0/sign=a1a1f9579058d109c4e3aeb4e963ab82/8b13632762d0f703ed7a0ff50afa513d2797c5cb.jpg)


几个重要的模块：
Spring Core：核心类库，所有的功能都依赖该类库，提供IOC与DI服务
Spring AOP：AOP服务
Spring ORM：对现有的ORM框架的支持
Spring Web：为创建Web应用程序提供支持
Spring JDBC：Java数据库连接

<h3>IOC</h3>

![](http://image.uc.cn/o/wemedia/s/upload/2021/fc83ea802e410d76b6713f81268a17be.png;,4,png;3,700x.png)
IOC 控制反转——————>是依赖倒置原则的一种代码设计思路---->采用的方法是依赖注入
依赖注入的3种方式：
* 构造方法传递
* Setting传递
* 接口传递

控制反转优点：
* 降低重复代码
* 隐藏细节

IOC实现原理
```java
interface Fruit {
    public abstract void eat();
}

class Apple implements Fruit {
    public void eat() {
        System.out.println("Apple");
    }
}

class Orange implements Fruit {
    public void eat() {
        System.out.println("Orange");
    }
}

class Factory {
    public static Fruit getInstance(String ClassName) {
        Fruit f = null;
        try {
            f = (Fruit) Class.forName(ClassName).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return f;
    }
}

class Client {
    public static void main(String[] a) {
        Fruit f = Factory.getInstance("io.github.dunwu.spring.Apple");
        if (f != null) {
            f.eat();
        }
    }
}
```

<h3>AOP(面向切面编程)</h3>
AOP的实现关键在代理模式，AOP代理分为静态代理（AspectJ基于字节码操作）与动态代理（Spring AOP基于代理）


<h3>Spring中的Bean的生命周期</h3>
实例化Instantiation--->属性赋值Populate--->初始化Initialization ---> 销毁Destruction
![](http://image.uc.cn/o/wemedia/s/upload/2021/a097fd438caecc310acad94ac255155b.png;,4,png;3,700x.png)



[Java面试宝典Beta5.0.pdf](http://blog.lutao1726.top/Java%E9%9D%A2%E8%AF%95%E5%AE%9D%E5%85%B8Beta5.0.pdf)
[编程之美.pdf](http://blog.lutao1726.top/%E7%BC%96%E7%A8%8B%E4%B9%8B%E7%BE%8E.pdf)






