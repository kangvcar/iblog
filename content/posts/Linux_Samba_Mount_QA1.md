---
title: "关于Linux上SAMBA服务的权限问题(普通挂载)"
date: "2018-05-22"
draft: false
toc: true
images:
tags: 
  - Linux
  - Samba
  - Q&A
---

> 简介:Samba服务用于在不同平台(windows,linux)之间共享文件和打印机等。

**注：本文不讨论multiuser多用户挂载的情况**

## 要求：

![image](http://upload-images.jianshu.io/upload_images/2640591-47ab7b0f19774128?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 安装配置：(在服务器节点server0配置，在客户端节点desktop0验证)

### 1.server0节点安装samba(服务主程序)和samba-client(提供smbpasswd用户管理工具)

```shell
[root@server0 ~]# yum -y install samba samba-client
```

### 2.配置防火墙放行samba服务

```shell
[root@server0 ~]# firewall-cmd --add-service=samba --permanent
success
[root@server0 ~]# firewall-cmd --reload
success
[root@server0 ~]# firewall-cmd --list-all
public (default, active)
  interfaces: eth0
  sources:
  services: dhcpv6-client samba
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
```

### 3.创建共享目录,并配置该共享目录的SELinux上下文

```shell
[root@server0 ~]# mkdir /common
[root@server0 ~]# chmod o+w /common
[root@server0 ~]# semanage fcontext -a -t samba_share_t '/common(/.*)?'
Full path required for exclude: net:[4026532580].
Full path required for exclude: net:[4026532580].
[root@server0 ~]# restorecon -RFvv /common/
Full path required for exclude: net:[4026532580].
Full path required for exclude: net:[4026532580].
restorecon reset /common context unconfined_u:object_r:default_t:s0->system_u:object_r:samba_share_t:s0
[root@server0 ~]# ls -ldZ /common
drwxr-xrwx. root root system_u:object_r:samba_share_t:s0 /common
```

### 4.修改配置文件/etc/samba/smb.conf

```shell
[root@server0 ~]# vim /etc/samba/smb.conf
workgroup = STAFF
security = user
[common]
        path = /common
        browseable = yes
        hosts allow = 172.25.0.0/24
        writable = no
        write list = brian
```

### 5.添加本地用户(bash为nologin),并添加进samba数据库进行管理(smbpass -a)

```shell
[root@server0 ~]# useradd -s /sbin/nologin rob
[root@server0 ~]# useradd -s /sbin/nologin brian
[root@server0 ~]# smbpasswd -a rob
New SMB password:
Retype new SMB password:
Added user rob.
[root@server0 ~]# smbpasswd -a brian
New SMB password:
Retype new SMB password:
Added user brian.
[root@server0 ~]# id rob
uid=1001(rob) gid=1001(rob) groups=1001(rob)
[root@server0 ~]# id brian
uid=1002(brian) gid=1002(brian) groups=1002(brian)
```

### 6.重新启动服务

```shell
[root@server0 ~]# systemctl restart smb nmb</pre>
```

---

## 客户端验证1：

### 1.安装cifs组件以支持samba协议类型的文件共享服务

```shell
[root@desktop0 ~]# yum -y install cifs-utils
```

在客户端desktop0创建两个用户rob=1002,brian=1001

```shell
[root@desktop0 ~]# tail -2 /etc/passwd
brian:x:1001:1001::/home/brian:/bin/bash
rob:x:1002:1002::/home/rob:/bin/bash
```

服务端的uid情况：rob=1001;brian=1002

客户端的uid情况：rob=1002;brian=1001

### 2.创建挂载点

```shell
[root@desktop0 ~]# mkdir /mnt/smbrob
[root@desktop0 ~]# mkdir /mnt/smbbrian
```

### 3.分别以rob,brian用户挂载到smbrob,smbbrian目录

```shell
[root@desktop0 ~]# mount -t cifs -o username=rob,password=redhat //server0/common /mnt/smbrob/
[root@desktop0 ~]# mount -t cifs -o username=brian,password=redhat //server0/common /mnt/smbbrian/
[root@desktop0 ~]# df -h
Filesystem        Size  Used Avail Use% Mounted on
//server0/common   10G  3.1G  7.0G  31% /mnt/smbrob
//server0/common   10G  3.1G  7.0G  31% /mnt/smbbrian
```

### 4.以root进入到/mnt/smbbrian目录进行权限测试

```shell
[root@desktop0 smbbrian]# echo 'brian' > test1
[root@desktop0 smbbrian]# ll
total 1024
-rw-r--r--. 1 rob rob 6 Jul 24 22:39 test1
```

#### 结论

- 用brian用户挂载的目录，本地root用户有可写可读权限

- 但是root所创建的文件所属者显示为rob(这是因为客户端的用户uid映射到了samba服务端的实际用户uid)

- 因为/mnt/smbbrian目录是用brian用户去挂载的,所以在这个目录下创建的所有文件都属于brian用户(brian用户在samba服务器端的用户uid是1002),但是此处所创建的文件所属者却显示为rob(因为samba只识别用户uid号,并不识别用户名;因为用户uid1002在客户端对应的用户是rob,所以就显示为了rob),实际上这个文件的真正所属者是samba服务端用户uid为1002的用户。

### 5.以root进入到/mnt/samrob目录进行权限测试

```shell
[root@desktop0 smbrob]# echo 'rob' > test2
bash: test2: Permission denied
[root@desktop0 smbrob]# cat test1
brian
```

#### 结论

- 用rob用户挂载的目录，本地root用户有只有可读权限，没有写入权限

---

## 客户端验证2

### 1.查看rob和brian在服务器端的id

```shell
[root@server0 ~]# id rob
uid=1001(rob) gid=1001(rob) groups=1001(rob)
[root@server0 ~]# id brian
uid=1002(brian) gid=1002(brian) groups=1002(brian)
```

### 2.删除验证1时在客户端自动创建的用户rob,brian

```shell
[root@desktop0 smbrob]# userdel -r rob
[root@desktop0 smbrob]# userdel -r brian
```

服务端的uid情况：rob=1001;brian=1002

客户端的uid情况：无rob,brian用户

### 3.挂载步骤和验证1一样

### 4.以root进入到/mnt/smbbrian目录进行权限测试

```shell
[root@desktop0 smbbrian]# echo 'brian222' > test3
[root@desktop0 smbbrian]# ll
total 2048
-rw-r--r--. 1 1002 1002 6 Jul 24 22:39 test1
-rw-r--r--. 1 1002 1002 9 Jul 24 23:02 test3
```

#### 结论

- 用brian用户挂载的目录，本地root用户有可写可读权限

- 因为brian用户在samba服务端的uid为1002,所以这里就显示为1002

- 再次验证了samba用uid来识别用户，而非用户名

### 5.以root进入到/mnt/samrob目录进行权限测试

```shell
[root@desktop0 smbrob]# echo 'rob222' > test4
bash: test4: Permission denied
[root@desktop0 smbrob]# cat test3
brian222
```

#### 结论

- 用rob用户挂载的目录，本地root用户有只有可读权限，没有写入权限

---

## 客户端验证3

### 1.查看rob和brian在服务器端的uid

```shell
[root@server0 ~]# id rob
uid=1001(rob) gid=1001(rob) groups=1001(rob)
[root@server0 ~]# id brian
uid=1002(brian) gid=1002(brian) groups=1002(brian)
```

### 2.删除在客户端自动创建的用户rob,brian(验证2以完成该操作);然后在客户端再创建两个用户rob,brian,且用户uid和samba服务端的用户uid相同(bash为默认/sbin/login)

```shell
[root@desktop0 smbrob]# useradd -u 1001 rob
[root@desktop0 smbrob]# useradd -u 1002 brian
[root@desktop0 smbrob]# id rob
uid=1001(rob) gid=1001(rob) groups=1001(rob)
[root@desktop0 smbrob]# id brian
uid=1002(brian) gid=1002(brian) groups=1002(brian)
```

服务端的uid情况：rob=1001;brian=1002

客户端的uid情况：rob=1001;brian=1002

### 3.挂载步骤和验证1一样

### 4.以root进入到/mnt/smbbrian目录进行权限测试

```shell
[root@desktop0 smbbrian]# echo 'brian555' > test5
[root@desktop0 smbbrian]# ll
total 3072
-rw-r--r--. 1 brian brian 6 Jul 24 22:39 test1
-rw-r--r--. 1 brian brian 9 Jul 24 23:02 test3
-rw-r--r--. 1 brian brian 9 Jul 24 23:17 test5
```

#### 结论

- 结论和实验2一样，只是所属者的uid1002对应上了本地用brian，所以就显示为brian了

### 5.以root进入到/mnt/samrob目录进行权限测试

```shell
[root@desktop0 smbrob]# echo 'rob666' > test6
bash: test6: Permission denied
[root@desktop0 smbrob]# cat test5
brian555
```

#### 结论

- 结论和验证2一样

### 6.以brian,rob,student进入到/mnt/smbbrian目录进行权限测试

```shell
[root@desktop0 ~]# su - brian
[brian@desktop0 ~]$ cd /mnt/smbbrian/
[brian@desktop0 smbbrian]$ echo 'brian777' > test7
[brian@desktop0 smbbrian]$ ll
total 4096
-rw-r--r--. 1 brian brian 6 Jul 24 22:39 test1
-rw-r--r--. 1 brian brian 9 Jul 24 23:02 test3
-rw-r--r--. 1 brian brian 9 Jul 24 23:17 test5
-rw-r--r--. 1 brian brian 9 Jul 24 23:24 test7

oot@desktop0 ~]# su - rob
[rob@desktop0 ~]$ cd /mnt/smbbrian/
[rob@desktop0 smbbrian]$ ll
total 4096
-rw-r--r--. 1 brian brian 6 Jul 24 22:39 test1
-rw-r--r--. 1 brian brian 9 Jul 24 23:02 test3
-rw-r--r--. 1 brian brian 9 Jul 24 23:17 test5
-rw-r--r--. 1 brian brian 9 Jul 24 23:24 test7
[rob@desktop0 smbbrian]$ echo 'rob888' > test8
-bash: test8: Permission denied
[rob@desktop0 smbbrian]$ cat test7
brian777

[root@desktop0 ~]# su - student
[student@desktop0 ~]$ cd /mnt/smbbrian/
[student@desktop0 smbbrian]$ eche 'student111' > file1
-bash: file1: Permission denied
[student@desktop0 smbbrian]$ ll
total 5120
-rw-r--r--. 1 brian brian 0 Jul 24 23:35 file1
-rw-r--r--. 1 brian brian 6 Jul 24 22:39 test1
-rw-r--r--. 1 brian brian 9 Jul 24 23:02 test3
-rw-r--r--. 1 brian brian 9 Jul 24 23:17 test5
-rw-r--r--. 1 brian brian 9 Jul 24 23:24 test7
-rw-r--r--. 1 brian brian 0 Jul 24 23:27 test8
-rw-r--r--. 1 brian brian 9 Jul 24 23:29 test9
```

#### 结论

- brian用户因为与samba服务器端brian用户相同的uid,所以该用户对/mnt/smbbrian目录权限为可读写

- rob用户因为与samba服务器端brian用户相同的uid,所以该用户对/mnt/smbbrian目录权限为只读

- student 没有写入权限,但是有创建文件的权限(虽然提示Permission denied,但还是成功创建文件了，但是文件里没有能写入内容)

### 7.以brian,rob,student进入到/mnt/smbrob目录进行权限测试

```shell
[root@desktop0 ~]# su - brian
[brian@desktop0 ~]$ cd /mnt/smbbrian/
[brian@desktop0 smbbrian]$ echo 'brian999' > test9
[brian@desktop0 smbbrian]$ ll
total 5120
-rw-r--r--. 1 brian brian 6 Jul 24 22:39 test1
-rw-r--r--. 1 brian brian 9 Jul 24 23:02 test3
-rw-r--r--. 1 brian brian 9 Jul 24 23:17 test5
-rw-r--r--. 1 brian brian 9 Jul 24 23:24 test7
-rw-r--r--. 1 brian brian 0 Jul 24 23:27 test8
-rw-r--r--. 1 brian brian 9 Jul 24 23:29 test9

[root@desktop0 ~]# su - rob
[rob@desktop0 ~]$ cd /mnt/smbrob/
[rob@desktop0 smbrob]$ echp 'rob1010' > test10
-bash: test10: Permission denied
[rob@desktop0 smbrob]$ cat test9
brian999

[root@desktop0 ~]# su - student
[student@desktop0 ~]$ cd /mnt/smbrob/
[student@desktop0 smbrob]$ echo 'student222' > file2
-bash: file2: Permission denied
[student@desktop0 smbrob]$ ll
total 5120
-rw-r--r--. 1 brian brian 0 Jul 24 23:35 file1
-rw-r--r--. 1 brian brian 6 Jul 24 22:39 test1
-rw-r--r--. 1 brian brian 9 Jul 24 23:02 test3
-rw-r--r--. 1 brian brian 9 Jul 24 23:17 test5
-rw-r--r--. 1 brian brian 9 Jul 24 23:24 test7
-rw-r--r--. 1 brian brian 0 Jul 24 23:27 test8
-rw-r--r--. 1 brian brian 9 Jul 24 23:29 test9
[student@desktop0 smbrob]$ cat file1
```

#### 结论

- brian用户因为与samba服务器端brian用户相同的uid,所以该用户对/mnt/smbbrian目录权限为可读写

- rob用户因为与samba服务器端brian用户相同的uid,所以该用户对/mnt/smbbrian目录权限为只读

- student 没有写入权限,也没有创建文件的权限,只有读权限

---


## 综上

- 在samba服务器端,权限由共享的目录的普通权限和smb.conf配置文件共同约束

- samba服务的认证数据在samba的数据库里,而非系统用户(用smbpasswd来管理samba用户数据)

- samba服务使用uid来识别用户,而非用户名

- 在客户端,所挂载的目录权限由所挂载的用户权限来决定(本地root用户除外)
