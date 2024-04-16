---
title: 设计模式(3)-抽象工厂模式
tags: [Java, 设计模式]
categories: 设计模式教程
date:  2018/5/30 16:41
---
<!-- more -->

抽象工厂模式（Abstract Factory）
--------------------------
>工厂方法模式有一个问题就是，类的创建依赖工厂类，也就是说，如果想要拓展程序，必须对工厂类进行修改，这违背了闭包原则，所以，从设计角度考虑，有一定的问题，如何解决？就用到抽象工厂模式，创建多个工厂类，这样一旦需要增加新的功能，直接增加新的工厂类就可以了，不需要修改之前的代码。因为抽象工厂不太好理解，我们先看看图，然后就和代码，就比较容易理解。

![Mou icon](http://dl2.iteye.com/upload/attachment/0083/1185/34a0f8de-16e0-3cd5-9f69-257fcb2be742.jpg?_=3023236)

Sender代码:
```java
    public interface Sender {
        public void Send();
    }
```
MailSender代码:
```java
    public class MailSender implements Sender {
    
        @Override
        public void Send() {
            System.out.println("this is MailSender");
        }
    }
```
SmsSender代码:
```java
    public class SmsSender implements Sender {
    
        @Override
        public void Send() {
            System.out.println("this is SmsSender");
        }
    }
```
Provider代码:
```java
    public interface Provider {
        public Sender produce();
    }
```
SendMailFactory代码:
```java
    public class SendMailFactory implements Provider {
    
        @Override
        public Sender produce() {
            return new MailSender();
        }
    }
```
SendSmsFactory代码:
```java
    public class SendSmsFactory implements Provider {
    
        @Override
        public Sender produce() {
            return new SmsSender();
        }
    }
    
```
Test代码:
```java
    public class Test {
    
        public static void main(String[] args){
            Provider provider = new SendMailFactory();
            Sender sender = provider.produce();
            sender.Send();
        }
    }
    
```

总结
------------
>其实这个模式的好处就是，如果你现在想增加一个功能：发及时信息，则只需做一个实现类，实现Sender接口，同时做一个工厂类，实现Provider接口，就OK了，无需去改动现成的代码。这样做，拓展性较好！
