---
title: "Git - git push origin master 报错的解决方法"
date: "2020-06-20"
draft: false
toc: true
images:
tags: 
  - Git
---

## 错误提示如下

``` shell 
[root@linux1 php]# git push -u origin master  
To git@github.com:kangvcar/Results-Systems--PHP.git  
 ! [rejected]        master -> master (fetch first)  
error: failed to push some refs to 'git@github.com:kangvcar/Results-Systems--PHP.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing 
hint: to the same ref. You may want to first merge the remote changes (e.g.,  
hint: 'git pull') before pushing again. 
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

## 原因

> GitHub远程仓库中的`README.md`文件不在本地仓库中。

## 解决方案1

我们只需加上 -f 参数即可push成功

```shell
[root@linux1 qimo]# git push -f  
warning: push.default is unset; its implicit value is changing in
 Git 2.0 from 'matching' to 'simple'. To squelch this message  
 and maintain the current behavior after the default changes, use:  
 
  git config --global push.default matching  
 
 To squelch this message and adopt the new behavior now, use:  
 
  git config --global push.default simple 
 
 See 'git help config' and search for 'push.default' for further information. 
 (the 'simple' mode was introduced in Git 1.7.11. Use the similar mode 
 'current' instead of 'simple' if you sometimes use older versions of Git) 
 
 Counting objects: 53, done. 
 Compressing objects: 100% (53/53), done. 
 Writing objects: 100% (53/53), 1.35 MiB | 55.00 KiB/s, done. 
 Total 53 (delta 8), reused 0 (delta 0) 
 remote: Resolving deltas: 100% (8/8), done. 
 To git@github.com:kangvcar/Results-Systems--PHP.git 
  + fbe05e8...70b187d master -> master (forced update)
```

## 解决方案2

我们只需加上 --rebase 参数然后再重新 push 一次即可

```shell
[root@linux1 qimo]# git pull --rebase origin master 
[root@linux1 qimo]# git push -u origin master
```