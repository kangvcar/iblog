---
title: "CentOS配置本地Yum源、阿里云Yum源、163Yum源、并配置Yum源的优先级"
date: "2020-02-11"
draft: false
toc: true
images:
tags: 
  - CentOS
  - 本地Yum源
  - 阿里云Yum源
  - 163Yum源
  - Yum源的优先级
---

# 一、用Centos镜像搭建本地Yum源
由于安装centos后的默认Yum源为centos的官方地址，所以在国内使用很慢甚至无法访问，所以一般的做法都是把默认的Yum源替换成aliyun的Yum源或者163等国内的Yum源(下文介绍如何配置)。 

但是以上的方法都是需要网络的，当没有网络的时候就无法使用了，所以还有一个常用的方法就是用Centos的ISO镜像搭建本地Yum源，这样安装软件的速度就会飞快，缺点是可能有些包没有。

1. 安装Centos后默认的Yum源如下

2. 查看默认Yum源文件

```shell
[root@kangvcar ~]# ll /etc/yum.repos.d/
total 32
-rw-r--r--. 1 root root 1664 Dec  9  2015 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Dec  9  2015 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Dec  9  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  290 Dec  9  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Dec  9  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 Dec  9  2015 CentOS-Sources.repo
-rw-r--r--. 1 root root 1952 Dec  9  2015 CentOS-Vault.repo
```

3. 把默认Yum源备份(可选)

```shell
[root@kangvcar ~]# mkdir /opt/centos-yum.bak
[root@kangvcar ~]# mv /etc/yum.repos.d/* /opt/centos-yum.bak/
```

4. 在虚拟机上挂载CentOS镜像文件

5. 挂载ISO镜像
![挂载ISO镜像](http://upload-images.jianshu.io/upload_images/2640591-51a1e9598dd31898?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

```shell
[root@kangvcar ~]# mount -t iso9660 /dev/sr0 /opt/centos
mount: /dev/sr0 is write-protected, mounting read-only
```

6. 编写repo文件并指向镜像的挂载目录

```shell
[root@kangvcar ~]# vi /etc/yum.repos.d/local.repo  
[local]
name=local
baseurl=file:///opt/centos
enabled=1
gpgcheck=0
```

7. 清除缓存

```shell
[root@kangvcar ~]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: local
Cleaning up everything
Cleaning up list of fastest mirrors
[root@kangvcar ~]# yum makecache        //把Yum源缓存到本地，加快软件的搜索好安装速度
[root@kangvcar ~]# yum list        //列出了3780个包
```

---

# 二、把默认的CentOS Yum源修改成国内的aliyun Yum源

> 阿里云官方教程：[http://mirrors.aliyun.com/help/centos](http://mirrors.aliyun.com/help/centos)

1. 安装Centos后默认的Yum源如下

```shell
[root@kangvcar ~]# ll /etc/yum.repos.d/
total 32
-rw-r--r--. 1 root root 1664 Dec  9  2015 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Dec  9  2015 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Dec  9  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  290 Dec  9  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Dec  9  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 Dec  9  2015 CentOS-Sources.repo
-rw-r--r--. 1 root root 1952 Dec  9  2015 CentOS-Vault.repo
```

2. 把默认Yum源备份(可选)

```shell
[root@kangvcar ~]# mkdir /opt/centos-yum.bak
[root@kangvcar ~]# mv /etc/yum.repos.d/* /opt/centos-yum.bak/
```

3. 下载aliyun Yum源repo文件(对应自己的系统版本下载即可)

```shell
#各系统版本repo文件对应的下载操作
CentOS 5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
CentOS 7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

```shell
[root@kangvcar ~]# cat /etc/redhat-release        //查看系统的版本
CentOS Linux release 7.2.1511 (Core)
[root@kangvcar ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
--2017-06-20 06:43:08--  http://mirrors.aliyun.com/repo/Centos-7.repo
Resolving mirrors.aliyun.com (mirrors.aliyun.com)... 112.124.140.210, 115.28.122.210
Connecting to mirrors.aliyun.com (mirrors.aliyun.com)|112.124.140.210|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2573 (2.5K) [application/octet-stream]
Saving to: ‘/etc/yum.repos.d/CentOS-Base.repo’
100%[=======================================================================================================>] 2,573       --.-K/s   in 0s      
2017-06-20 06:43:08 (118 MB/s) - ‘/etc/yum.repos.d/CentOS-Base.repo’ saved [2573/2573]
```

4. 清除缓存

```shell
[root@kangvcar ~]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: base extras updates
Cleaning up everything
Cleaning up list of fastest mirrors
[root@kangvcar ~]# yum makecache        //把Yum源缓存到本地，加快软件的搜索好安装速度
[root@kangvcar ~]# yum list        //总共列出了9954个包
```

---

# 三、把默认的CentOS Yum源修改成国内的163源

> 163官方教程：[http://mirrors.163.com/.help/centos.html](http://mirrors.163.com/.help/centos.html)

1. 安装Centos后默认的Yum源如下

```shell
[root@kangvcar ~]# ll /etc/yum.repos.d/
total 32
-rw-r--r--. 1 root root 1664 Dec  9  2015 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Dec  9  2015 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Dec  9  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  290 Dec  9  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Dec  9  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 Dec  9  2015 CentOS-Sources.repo
-rw-r--r--. 1 root root 1952 Dec  9  2015 CentOS-Vault.repo
```

2. 把默认Yum源备份(可选)

```shell
[root@kangvcar ~]# mkdir /opt/centos-yum.bak
[root@kangvcar ~]# mv /etc/yum.repos.d/* /opt/centos-yum.bak/
```

3. 下载163 Yum源repo文件

```shell
#各系统版本repo文件对应的下载操作
CentOS 5
wget -O /etc/yum.repos.d/CentOS5-Base-163.repo http://mirrors.163.com/.help/CentOS5-Base-163.repo
CentOS 6
wget -O /etc/yum.repos.d/CentOS6-Base-163.repo http://mirrors.163.com/.help/CentOS6-Base-163.repo
CentOS 7
wget -O /etc/yum.repos.d/CentOS7-Base-163.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

```shell
[root@kangvcar ~]# cat /etc/redhat-release        //查看系统的版本
CentOS Linux release 7.2.1511 (Core)
[root@kangvcar ~]# wget -O /etc/yum.repos.d/CentOS7-Base-163.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
--2017-06-20 06:29:47--  http://mirrors.163.com/.help/CentOS7-Base-163.repo
Resolving mirrors.163.com (mirrors.163.com)... 123.58.173.185, 123.58.173.186
Connecting to mirrors.163.com (mirrors.163.com)|123.58.173.185|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1572 (1.5K) [application/octet-stream]
Saving to: ‘/etc/yum.repos.d/CentOS7-Base-163.repo’
100%[=======================================================================================================>] 1,572       --.-K/s   in 0s      
2017-06-20 06:29:47 (293 MB/s) - ‘/etc/yum.repos.d/CentOS7-Base-163.repo’ saved [1572/1572]
```

4. 清除缓存

```shell
[root@kangvcar ~]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: base extras updates
Cleaning up everything
Cleaning up list of fastest mirrors
[root@kangvcar ~]# yum makecache        //把Yum源缓存到本地，加快软件的搜索好安装速度
[root@kangvcar ~]# yum list        //总共列出了9951个包
```

# 四、修改Yum源的优先级

> 当既有本地Yum源又有163源的时候，我们在装软件包的时候当然希望先用本地的Yum源去安装，本地找不到可用的包时再使用163源去安装软件,这里就涉及到了优先级的问题，Yum提供的插件yum-plugin-priorities.noarch可以解决这个问题

1. 查看系统是否安装了优先级的插件

```shell
[root@kangvcar ~]# rpm -qa | grep yum-plugin-
yum-plugin-fastestmirror-1.1.31-34.el7.noarch        
//这里看到没有安装yum-plugin-priorities.noarch这个插件
[root@kangvcar ~]# yum search yum-plugin-priorities        
//用search查看是否有此插件可用
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
* base: mirrors.aliyun.com
* extras: mirrors.aliyun.com
* updates: mirrors.aliyun.com
====================================================== N/S matched: yum-plugin-priorities =======================================================
yum-plugin-priorities.noarch : plugin to give priorities to packages from different repos
```

2. 安装yum-plugin-priorities.noarch插件

```shell
[root@kangvcar ~]# yum -y install yum-plugin-priorities.noarch
```

3. 查看插件是否启用

```shell
[root@kangvcar ~]# cat /etc/yum/pluginconf.d/priorities.conf
[main]
enabled = 1
//1为启用；0为禁用
```

4. 修改本地Yum源优先使用

```shell
[root@kangvcar ~]# ll /etc/yum.repos.d/
total 8
-rw-r--r--. 1 root root 2573 May 15  2015 CentOS-Base.repo
-rw-r--r--. 1 root root   67 Jun 20 06:04 local.repo
//有两个repo文件
[root@kangvcar ~]# vi /etc/yum.repos.d/local.repo
[local]
name=local
baseurl=file:///opt/centos
enabled=1
gpgcheck=0
priority=1
//在原基础上加入priority=1 ；数字越小优先级越高
//可以继续修改其他源的priority值，经测试仅配置本地源的优先级为priority=1就会优先使用本地源了
```

5. 测试

```shell
配置优先级前：(使用阿里云Yum源)
[root@kangvcar ~]# yum -y install vim
Dependencies Resolved
=================================================================================================================================================
Package                            Arch                         Version                                     Repository                     Size
=================================================================================================================================================
Installing:
vim-enhanced                       x86_64                       2:7.4.160-1.el7_3.1                         updates                       1.0 M
Updating for dependencies:
vim-common                         x86_64                       2:7.4.160-1.el7_3.1                         updates                       5.9 M
省略···
```

```shell
配置优先级后：(使用本地Yum源)
[root@kangvcar ~]# yum -y install vim
Dependencies Resolved
=================================================================================================================================================
Package                                     Arch                        Version                                Repository                  Size
=================================================================================================================================================
Installing:
vim-enhanced                                x86_64                      2:7.4.160-1.el7                        local                      1.0 M
Installing for dependencies:
gpm-libs                                    x86_64                      1.20.7-5.el7                           local                       32 k
perl                                        x86_64                      4:5.16.3-286.el7                       local                      8.0 M
perl-Carp                                   noarch                      1.26-244.el7                           local                       19 k
perl-Encode                                 x86_64                      2.51-7.el7                             local                      1.5 M
perl-Exporter                               noarch                      5.68-3.el7                             local                       28 k
perl-File-Path                              noarch                      2.09-2.el7                             local                       26 k
perl-File-Temp                              noarch                      0.23.01-3.el7                          local                       56 k
省略···
```
