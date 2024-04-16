---
title: Java基础回顾-多线程(1)
tags: [Java, Java基础]
categories: Java教程
date:  2017/9/25 17:29
---
<!-- more -->
 Java 实现多线程的3种方法:继承Thread类;实现Runnable接口;实现Callable接口
 ============================
 众所周知实现线程的方法具体有2种,但是还有一种,不是所有的人都知道。
 > (1).继承Thread类，重写run()方法；   
 > (2).实现Runnable接口，重写run()方法；    
 > (3).实现Callable接口，重写call方法；

第一种实现方式：继承Thread类，重写run方法
------------------------------
```Java
public class ThreadWay extends Thread{
  @Override
  public void run(){
    for(;;){
      try{
        Thread.sleep(1000);//毫秒  
        System.out.println(Thread.currentThread().getName() + "...extends way");
      }catch(Exception e){
        e.printStackTrace();  
      }
    }
  }

}
```
第二种实现方式：实现Runnable接口，重写run()方法；   
------------------------------
```Java
public class RunnableWay implements Runnable{
  @Override  
    public void run() {  
        for (; ; ) {  
            try {  
                Thread.sleep(1000);//毫秒  
                System.out.println(Thread.currentThread().getName() + "...implements Runnable way");  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
        }  
    }
}
```
第三种实现方式：实现Callable接口，重写call方法；
------------------------------
```Java
/**
 * Callable和Runnable有几点不同：
 * （1）Callable规定的方法是call()，而Runnable规定的方法是run().
 * （2）Callable的任务执行后可返回值，而Runnable的任务是不能返回值的。
 * （3）call()方法可抛出异常，而run()方法是不能抛出异常的。
 * （4）运行Callable任务可拿到一个Future对象，
 * <p>
 */  
public class CallableWay implements Callable<Integer> {  

    @Override  
    public Integer call() throws Exception {  
        int sum = 0;  
        for (int i = 0; i < 10; i++) {  
            try {  
                Thread.sleep(1000);//毫秒  
                sum += 1;  
                System.out.println(Thread.currentThread().getName() + "...implements Callable<T>..." + sum);  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
        }  
        return sum;  
    }  
}  
```
当然第三种并不常见，目前只是简单的记录下
```Java
/**
 * 线程测试（实现多线程的几种方式）
 * <p> 
 */  
public class Main {  
    public static void main(String[] args) {  
        newThreadWay1();//实现Runnable接口  
        //newThreadWay2();//继承Thread类  
        //newThreadWay3();//实现Callable接口  
        //newThreadWay1_();//也就是个简单的匿名对象，直接实现Runnable接口，省去了新建个类的步骤  
        //newThreadWay1__();//一样也是实现Runnable接口，省去了新建个类的步骤，不过不是匿名对象而已  
    }  

    /**
     * 创建线程方式1实现
     * 实现Runnable接口，重写run方法
     * 实现接口的优点：可以方便扩展
     */  
    private static void newThreadWay1() {  
        ThreadWay1 threadWay1 = new ThreadWay1();  
        new Thread(threadWay1).start();  
        //new Thread(new ThreadWay1()).start();//等于上面的2行代码  
        System.out.println(Thread.currentThread().getName() + "...implements Runnable way");  
    }  


    /**
     * 创建线程方式2实现
     * 继承Thread类，重写run函数
     */  
    private static void newThreadWay2() {  
        ThreadWay2 threadWay2 = new ThreadWay2();  
        threadWay2.start();  
        //new ThreadWay2().start();//等于上面的2行代码  
        System.out.println(Thread.currentThread().getName() + "...extends way");  
    }  

    /**
     * 创建线程方式3实现
     * 实现Callable接口，重写call方法
     */  
    private static void newThreadWay3() {  
        int resultFromThread;  
        try {  
            ThreadWay3 threadWay3 = new ThreadWay3();  
            FutureTask<Integer> future = new FutureTask<>(threadWay3);  
            new Thread(future).start();  
            Thread.sleep(5000);// 可能做一些事情  
            //注意：  
            // 这个有个问题：主线程必须等子线程执行完，才可以继续执行，变成了同步啦，这就不太好啦。失去了多线程的意义啦。  
            resultFromThread = future.get();  
            System.out.println(Thread.currentThread().getName() + "...implements Callable<T>..." + resultFromThread);  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  

    /**
     * 创建线程方式1实现
     * 实现Runnable接口，重写run方法
     * (匿名对象：new个线程对象直接开启)
     */  
    private static void newThreadWay1_() {  
        new Thread(new Runnable() {  
            @Override  
            public void run() {  
                for (; ; ) {//死循环  
                    try {  
                        Thread.sleep(1000);//毫秒  
                        System.out.println(Thread.currentThread().getName() + "...new Runnable()匿名对象");  
                    } catch (InterruptedException e) {  
                        e.printStackTrace();  
                    }  
                }  

            }  
        }).start();  
        System.out.println(Thread.currentThread().getName() + "......new Runnable()匿名对象");  
    }  

    /**
     * 创建线程方式1实现
     * 实现Runnable接口，重写run方法
     * (实例化一个线程对象，然后调用start方法开启线程)
     */  
    private static void newThreadWay1__() {  
        Thread thread = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                for (; ; ) {  
                    try {  
                        Thread.sleep(1000);//毫秒  
                        System.out.println(Thread.currentThread().getName() + "...new Runnable()非匿名对象");  
                    } catch (InterruptedException e) {  
                        e.printStackTrace();  
                    }  
                }  
            }  
        });  
        thread.start();  
        System.out.println(Thread.currentThread().getName() + "...new Runnable()非匿名对象");  
    }  
}  
```


