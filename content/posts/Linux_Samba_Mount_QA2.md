---
title: "关于Linux上SAMBA服务的权限问题(多用户挂载)"
date: "2018-05-23"
draft: false
toc: true
images:
tags: 
  - Linux
  - Samba
  - Q&A
---

> 简介：本文探讨关于Samba服务在客户端进行多用户挂载的问题,以及对cifscreds命令的使用效果.

## 安装配置

(服务器配置要求如下,配置过程在本博客中<关于Linux上SAMBA服务的权限问题(普通挂载)>,本文省略配置过程)

![要求](http://upload-images.jianshu.io/upload_images/2640591-366bdb404f0b6086?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

## 在客户端desktop0配置进行多用户挂载：

### 1.安装cifs-utils软件包以支持samba的挂载(否则即使挂载没有报错也无法正常使用)

```shell
[root@desktop0 ~]# yum -y install cifs-utils
```

### 2.创建挂载点

```shell
[root@desktop0 ~]# mkdir /mnt/multiuser
```

### 3.把挂载条目写入/etc/fstab以使开机自动挂载

```shell
[root@desktop0 ~]# vim /etc/fstab
//server0/common    /mnt/multiuser    cifs    credentials=/root/smb.cred,multiuser，sec=ntlmssp,_netdev,defaults    0 0
```

注解：

- credentials=/root/smb.cred 用于指定包含挂载用户信息文件,此用户一般是samba服务器里对共享目录具有较低权限的用户(本文使用的rob用户对共享目录权限为只读),文件包含username,password

- multiuser 关键选项,用于指定使用多用户挂载

- sec=ntlmssp 指定认证方式(kernel3.8之后是默认选项)

- _netdev 关键选项,否则系统会启动失败;不写入/etc/fstab文件时，次选项可省略

- defaults 用于指定挂载后的默认属性，但权限还受samba服务器约束

### 4.创建并编写/etc/smb.cred文件,出于安全性考虑应该修改此文件的权限为600

```shell
[root@desktop0 ~]# vim /root/smb.cred
username=rob
password=redhat
[root@desktop0 ~]# chmod 600 /root/smb.cred
```

### 5.执行挂载操作

```shell
[root@desktop0 ~]# mount -a
[root@desktop0 ~]# mount | grep /mnt/multiuser
//server0/common on /mnt/multiuser type cifs (rw,relatime,vers=1.0,sec=ntlmssp,cache=strict,multiuser,domain=SERVER0,uid=0,noforceuid,gid=0,noforcegid,addr=172.25.0.11,unix,posixpaths,serverino,acl,noperm,rsize=1048576,wsize=65536,actimeo=1)
```

### 6.进入挂载点进行权限测试

```shell
[root@desktop0 ~]# cd /mnt/multiuser/
[root@desktop0 multiuser]# touch 11
touch: cannot touch ‘11’: Permission denied
```

#### 结论

- 文件的挂载用户是rob,故只有只读权限,没有写入权限

### 7.在root用户下使用cifscreds命令把指定用户的用户名和密码加入内核密钥环

```shell
[root@desktop0 ~]# cifscreds add server0 -u brian
Password:redhat
[root@desktop0 ~]# cd /mnt/multiuser/
[root@desktop0 multiuser]# touch 22
touch: cannot touch ‘22’: Permission denied
```

#### 结论

- 由于一开始是root用户以rob用户的角色进行目录的挂载(root的环境变量已改变)

- 再在root用户下使用cifscreds命令把指定用户的用户名和密码加入内核密钥环会不生效

### 8.在其他用户下使用cifscreds命令把指定用户的用户名和密码加入内核密钥环

```shell
[root@desktop0 ~]# su - user1
[user1@desktop0 ~]$ cifscreds add server0 -u brian
Password:
[user1@desktop0 ~]$ cd /mnt/multiuser/
[user1@desktop0 multiuser]$ touch 33
#####在user1用户下把brian用户的用户名和密码加入内核密钥环后成功获得写入权限#####成功
[user1@desktop0 multiuser]$ cd
[user1@desktop0 ~]$ cifscreds clearall
[user1@desktop0 ~]$ cifscreds add server0 -u rob
Password:
[user1@desktop0 ~]$ cd /mnt/multiuser/
[user1@desktop0 multiuser]$ touch 44
#####在user1用户下把rob用户的用户名和密码加入内核密钥环后效果并没有改变,还是有写入权限,正常应该只有读权限#####失败,没有生效
```

#### 结论

- 切换到其他用户后(除root外),第一次使用cifscreds命令把brian(rw)的用户名和密码加入内核密钥环,成功地获得了brian(rw)所对应的权限

- 第二次使用cifscreds命令把rob(ro)的用户名和密码加入内核密钥环,并没有获得rob(ro)所对应的权限,此时实际权限还是第一次加入密钥环的brian用户所对应的权限

- 经实验,使用cifscreds clearall 命令清除内核密钥环的数据后不会马上生效,大概5分钟后才回清除,此后可以成功把其他用户加入到内核密钥环中并生效

- 切换到其他用户(如:su - user2),第一次把指定的samba用户名和密码加入到内核密钥环中即可成功,但是第二次加入内核密钥环的信息会不生效(解决方法如上一条结论)
