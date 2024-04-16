---
title: 设计模式(2)-工厂模式
tags: [Java, 设计模式]
categories: 设计模式教程
date:  2018/5/25 16:41
---
<!-- more -->

工厂方法模式（Factory Method）
--------
工厂方法模式分为三种：
* `普通工厂模式`
  >就是建立一个工厂类，对实现了同一接口的一些类进行实例的创建。首先看下关系图：

![Mou icon](http://dl2.iteye.com/upload/attachment/0083/1180/421a1a3f-6777-3bca-85d7-00fc60c1ae8b.png?_=3023236)
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
SendFactory代码:
```java
    public class SendFactory {
        public Sender produce(String type){
            if("mail".equals(type)){
                return new MailSender();
            }else if("sms".equals(type)){
                return new SmsSender();
            }else{
                System.out.println("请输入正确的类型!");
                return  null;
            }
        }
    }
```
FactoryTest代码:
```java
    public class FactoryTest {
        public static  void  main(String[] args){
            SendFactory factory = new SendFactory();
            Sender sender = factory.produce("sms");
            sender.Send();
        }
    }
```


* `多个工厂方法模式`
  >是对普通工厂方法模式的改进，在普通工厂方法模式中，如果传递的字符串出错，则不能正确创建对象，而多个工厂方法模式是提供多个工厂方法，分别创建对象。关系图：

![Mou icon](http://dl2.iteye.com/upload/attachment/0083/1181/84673ccf-ef89-3774-b5cf-6d2523cd03e5.jpg?_=3023236)
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
SendFactory代码:
```java
    public class SendFactory {
        public Sender produceMail(){
            return new MailSender();
        }
        public Sender produceSms(){
            return new SmsSender();
        }
    }
```
FactoryTest代码:
```java
    public class FactoryTest {
        public static  void  main(String[] args){
            SendFactory factory = new SendFactory();
            Sender sender = factory.produceMail();
            sender.Send();
        }
    }
```


* `静态工厂模式`
  >将上面的多个工厂方法模式里的方法置为静态的，不需要创建实例，直接调用即可。

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
SendFactory代码:
```java
    public class SendFactory {
        public static Sender produceMail(){
            return new MailSender();
        }
        public static Sender produceSms(){
            return new SmsSender();
        }
    }

```
FactoryTest代码:
```java
    public class FactoryTest {
        public static  void  main(String[] args){
            Sender sender = SendFactory.produceMail();
            sender.Send();
        }
    }
```
总结
-----------------
  >总体来说，工厂模式适合：凡是出现了大量的产品需要创建，并且具有共同的接口时，可以通过工厂方法模式进行创建。在以上的三种模式中，第一种如果传入的字符串有误，不能正确创建对象，第三种相对于第二种，不需要实例化工厂类，所以，大多数情况下，我们会选用第三种——静态工厂方法模式。
