---
title: Java关于远程调试程序教程
date:  2019/9/29 17:29
categories: Java教程
tags: [ Java , Java基础]

---
<!-- 展示图片 -->
![Mou_icon](http://biddingcs.lutao1726.top/java-flag-001.jpg)
<!-- more -->

写一个简单程序打成jar丢到远程服务器运行，模拟远程Server在运行。就拿Java调用shell脚本提交作业程序为例分析。源码如下  
```java
import java.io.InputStream;

public class JavaShell {

    public static void main(String[] args) throws Exception {

        try {
            String grant = "chmod u+x submit-job.sh";
            Runtime runtime = Runtime.getRuntime();

            Process grantProc = runtime.exec(grant);
            int resultCode = grantProc.waitFor();
            System.out.println(resultCode);
            grantProc = runtime.exec("./submit-job.sh");
            resultCode = grantProc.waitFor();

            System.out.println(resultCode);
            InputStream in = grantProc.getInputStream();
            byte[] buffer = new byte[1024];
            int code;
            while ((code = in.read(buffer, 0, buffer.length)) != -1) {
                System.out.print(new String(buffer, 0, code));

            }
            
            /**
             * 死循环阻止debugger没有连接上之前程序退出（测试suspend参数功能） 
             */
            System.out.println("shell脚本执行完毕，接下来开始进行定时打印任务！");
            int i = 0;
            while (true) {
                Thread.sleep(2000);
                System.out.println("这是第" + (++i) + "次循环！");
            }

        } catch (Exception e) {
            System.out.println("this is a excption !");
        } finally {

        }

    }

}
```
打成Jar包提交到远程服务器之后运行：
```bash
java  -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,address=9999,server=y,suspend=y  -jar  JavaShell.jar 
```
![](http://blog.lutao1726.top/java-debug-01.png)

会发现程序阻塞，并等待debugger连接，此时我们可以使用eclipse进行远程调试：
![](http://blog.lutao1726.top/java-debug-02.png)

点击debugger即可进行连接，跟踪源码运行：
![](http://blog.lutao1726.top/java-debug-03.png)

远程有输出，证明远程程序正在跟踪debugger执行：
![](http://blog.lutao1726.top/java-debug-04.png)

更多参数细节：
```bash
-XDebug               启用调试。
-Xnoagent             禁用默认sun.tools.debug调试器。
-Djava.compiler=NONE  禁止 JIT 编译器的加载。
-Xrunjdwp             加载JDWP的JPDA参考执行实例。
transport             用于在调试程序和 VM 使用的进程之间通讯。
dt_socket             套接字传输。
dt_shmem              共享内存传输，仅限于 Windows。
server=y/n            VM 是否需要作为调试服务器执行。
address=3999          调试服务器的端口号，客户端用来连接服务器的端口号。
suspend=y/n           是否在调试客户端建立连接之后启动 VM 。
```

#### **IntelliJ IDEA远程调试运行中的JAVA程序/项目**
###### IntelliJ IDEA配置
* 1.添加一个运行配置（remote项）
![](http://blog.lutao1726.top/java-debug-05.png)
* 2.打开remote项配置对话框
* 3.远程jvm参数配置提示
* 4.远程调试的ip地址和端口号，ip就是java项目所在机器ip，端口只要不被占用就可以（注意防火墙不阻止该端口的访问）
* 5.源码模块，选择程序对应的源码模块即可

###### 远程java程序配置
* 1.普通java程序配置

示例：
```bash
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5006 -jar chess-server.jar
```

* 2.tomcat中web项目配置

在tomcat的bin目录中，新建setenv.sh文件，输入：
``` bash
CATALINA_OPTS="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5006"
```
 

如果是windows系统，新建setenv.bat文件，输入：
```bash
SET CATALINA_OPTS=-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5006
```
tomcat启动后会自动调用setenv文件，进行jvm参数设置

 

###### 启动调试

点击调试按钮，控制台输出如下提示就成功了。
```bash
Connected to the target VM, address: '192.168.66.252:5006', transport: 'socket' 
```
然后先在代码处打上断点，然后操作java程序即可进入断点。
  




