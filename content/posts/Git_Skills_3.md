---
title: "Git - 将本地仓库与远程仓库关联并推送至远程仓库"
date: "2020-06-22"
draft: false
toc: true
images:
tags: 
  - Git
---

# 把本地仓库推送到Github

## 1.配置公钥

```shell
[root@kangvcar ~]# ssh-keygen -t rsa -C "kangvcar@126.com" 
[root@kangvcar ~]# cat .ssh/id_rsa.pub
# 注意：在本地用ssh-keygen生成密钥对后，把公钥添加到github上
```

## 2.安装git服务

```shell
[root@kangvcar ~]# yum -y install git
```

## 3.配置基本环境参数

```shell
[root@kangvcar ~]# git config --global user.name "kangvcar" 
[root@kangvcar ~]# git config --global user.email "kangvcar@126.com"
```

## 4.创建本地仓库目录并初始化

```shell
[root@kangvcar ~]# mkdir kangvcar 
[root@kangvcar ~]# cd kangvcar/ 
[root@kangvcar kangvcar]# git init 
[root@kangvcar kangvcar]# echo "ni hao kangvcar" >> test.txt
```

## 5.将项目的所有文件添加到暂存区中，并commit

```shell
[root@kangvcar kangvcar]# git add . 
[root@kangvcar kangvcar]# git commit -m "add test.txt"
```

## 6.到Github上创建自己的Repository，名字可以不同

## 7.进入到新建的Repository，获取项目的地址

*如地址：git@github.com:kangvcar/kangvcar.git*

## 8.将本地仓库与远程仓库关联

```shell
[root@kangvcar kangvcar]# git remote add origin git@github.com:kangvcar/kangvcar.git
```

## 9.将本地仓库推送到github上

```shell
[root@kangvcar kangvcar]# git push -u origin master 
Warning: Permanently added the RSA host key for IP address '192.30.255.112' to the list of known hosts. 
Counting objects: 3, done. 
Writing objects: 100% (3/3), 222 bytes | 0 bytes/s, done. 
Total 3 (delta 0), reused 0 (delta 0) To git@github.com:kangvcar/kangvcar.git 
* [new branch]      master -> master Branch master set up to track remote branch master from origin.
```

## 10.在github上就能看到推送的更新了