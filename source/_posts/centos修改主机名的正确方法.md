---
title: centos修改主机名的正确方法
date:  2018/6/19 16:41
tags: [Linux, 运维]
categories: Linux教程
---
<!-- more -->
1.centos6下修改hostname
------------------------
```bash
[root@centos6 ~]$ hostname                                              # 查看当前的hostnmae
centos6.magedu.com
[root@centos6 ~]$ vim /etc/sysconfig/network                            # 编辑network文件修改hostname行（重启生效）
[root@centos6 ~]$ cat /etc/sysconfig/network                            # 检查修改
NETWORKING=yes
HOSTNAME=lutao1726.top
[root@centos6 ~]$ hostname lutao1726.top                                # 设置当前的hostname(立即生效）
[root@centos6 ~]$ vim /etc/hosts                                        # 编辑hosts文件，给127.0.0.1添加hostname
[root@centos6 ~]$ cat /etc/hosts                                        # 检查
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4 lutao1726.top
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
```

2.centos7下修改hostname
------------------------
```bash
[root@centos7 ~]$ hostnamectl set-hostname lutao1726.top                   # 使用这个命令会立即生效且重启也生效
[root@centos7 ~]$ hostname                                                 # 查看下
lutao1726.top
[root@centos7 ~]$ vim /etc/hosts                                           # 编辑下hosts文件， 给127.0.0.1添加hostname
[root@centos7 ~]$ cat /etc/hosts                                           # 检查
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 lutao1726.top
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```