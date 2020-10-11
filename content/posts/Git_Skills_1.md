---
title: "Git 检查&撤销修改"
date: "2020-06-20"
draft: false
toc: true
images:
tags: 
  - Git
---

> 说明：本教程的所有操作都在master分支上，且仅用于个人代码仓库管理，操作的实用性有待研究。

## 4个区

![Git 的检查修改和撤销修改](http://upload-images.jianshu.io/upload_images/2640591-58dffa2b008d361a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 5种状态

- 未修改（Origin）
- 已修改（Modified）
- 已暂存（Staged）
- 已提交（Committed）
- 已推送（Pushed）

## 检查修改

1. 已修改，未暂存（检查工作区与暂存区间的差异）

   ```shell
   git diff
   ```

2. 已暂存，未提交（检查暂存区与本地仓库间的差异）

   ```shell
   git diff --cached
   ```

3. 已提交，未推送（检查本地仓库与远程仓库间的修改）

   ```shell
   git diff master origin/master
   ## origin/master 为远程仓库
   ```

## 撤销修改

1. 已修改，未暂存（撤销工作区的修改）

   ```shell
   git reset --hard
   ```

2. 已暂存，未提交（撤销暂存区的修改）

   ```shell
   git reset --hard
   ```

3. 已提交，未推送（撤销本地仓库的修改）

   ```shell
   git reset --hard origin/master
   ## origin/master 为把远程仓库的代码取回并覆盖本地代码
   ```

4. 已推送（撤销远程仓库的修改）

   ```shell
   git reset --hard HEAD^
   git push -f
   ## 此时本地仓库和远程仓库是一样的
   ## 1.第一步先恢复本地仓库
   ## 2.第二步再强制同步本地仓库到远程仓库
   ```