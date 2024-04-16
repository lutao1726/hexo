---
title: gitlab安装与汉化
tags: [Linux, 运维, Java]
categories: Linux教程
date:  2018/7/05 16:41
---
<!-- more -->

gitlab安装与汉化相关资料
===============
环境说明：
虚拟机 centos7 64位
内存：3GB
存储：20GB

版本：gitlab 10.0.3

配置基础环境
------
安装wget
> sudo yum -y install wget

配置阿里巴巴 yum 源
> wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

重建yum 的缓存
>   yum clean all
    yum makecache

安装 git
> yum install -y git

创建 gitlab汉化版的源码目录，等一下使用git clone 一份新的代码
>mkdir gitlab_zh
 cd gitlab_zh
 git clone https://gitlab.com/xhang/gitlab.git

安装 gitlab 的依赖包
>   yum install curl openssh-server openssh-clients postfix cronie policycoreutils-python –y
    yum install -y patch

启动 postfix，并且设置为开机启动
>   systemctl start postfix
 systemctl enable postfix

 设置防火墙
>firewall-cmd --add-service=http --permanent
firewall-cmd --reload

gitlab安装
--------

大家从网上下载好 gitlab 的rpm 包后，就可以安装了
gitlab 下载地址：
>https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/

安装：
>rpm -i gitlab-ce-10.0.3-ce.0.el7.x86_64.rpm

当 shell 中出现以下内容，则证明 gitlab 的程序安装完了

![](../images/gitlab-01.png)

继续安装提示执行配置 gitlab 的命令
>gitlab-ctl reconfigure

修改 gitlab 的配置文件
> vi /etc/gitlab/gitlab.rb

修改里面的 external_url 参数，例如我的机器的IP地址为 192.168.1.4
>external_url='http://192.168.1.4'

重新加载配置文件
>gitlab-ctl reconfigure

gitlab汉化
--------
停止 gitlab 服务
> gitlab-ctl stop

切换到刚才下载的汉化包目录
> cd /root/source/gitlab_zh/gitlab/

找出安装的 10.0.3 版本和 汉化版本10.0.4 中的不同点
>git diff v10.0.3 v10.0.4-zh > ../10.0.3-zh.diff

目录倒退一层
>cd ../

将 10.0.3-zh.diff 的补丁添加到 gitlab 中
>patch -d /opt/gitlab/embedded/service/gitlab-rails -p1 < 10.0.3-zh.diff

重新配置 gitlab
>gitlab-ctl reconfigure

重新启动 gitlab 服务
>gitlab-ctl restart

浏览器访问gitlab
> http://192.168.1.4:80

![](../images/gitlab-02.png)


gitlab报错处理
----------
1.登录502报错
一般是权限问题，解决方法：
>chmod -R 755 /var/log/gitlab

如果还不行，请检查你的内存，安装使用GitLab需要至少4GB可用内存,少于4GB内存会出现各种诡异的问题, 而且在使用过程中也经常会出现500错误.



参考资料：
    https://www.cnblogs.com/wenwei-blog/p/5861450.html
    http://www.cnblogs.com/lt1726/