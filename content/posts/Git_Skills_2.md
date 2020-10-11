---
title: "Git - 如何修改开源项目并推送给作者or克隆远程仓库到本地"
date: "2020-06-21"
draft: false
toc: true
images:
tags: 
  - Git
---

# 克隆远程仓库到本地

1. 配置公钥

```shell
[root@kangvcar ~]# ssh-keygen -t rsa -C "kangvcar@126.com"
[root@kangvcar ~]# cat .ssh/id_rsa.pub
# 注意：在本地用ssh-keygen生成密钥对后，把公钥添加到github上
```

2. 安装git服务

```shell
[root@kangvcar ~]# yum -y install git
```

3. 配置基本环境参数

```shell
[root@kangvcar ~]# git config --global user.name "kangvcar"
[root@kangvcar ~]# git config --global user.email "kangvcar@126.com"
```

4. 克隆远程仓库到本地

```
[root@kangvcar ~]# git clone git@github.com:kangvcar/cprogramming.git
# 注意： git@github.com:kangvcar/cprogramming.git 地址在github上复制而得，
# 执行成功后远程仓库会克隆到本地当前目录下名为的cprogramming的文件夹
```

***以上前4步的操作是克隆自己github里的项目到本地***

---

# 如何修改别人的项目并提交给原作者？

> 因为我只能把我本地的公钥添加到了我的github上，而不能添加到别人的github账户下，所以只能克隆自己github仓库里的项目，而不能clone别人仓库里的项目。那么如果我们想修改别人的项目然后自己做完修改后提交回给原作者该怎么做呢？首先肯定不能直接克隆别人的github项目的地址，因为你没有公钥在别人的github仓库里，所以我要像把别人的项目fork到自己的github上，然后在克隆到本地，在本地修改完成后，在提交给原作者。

具体操作步骤如下：
1. 先用网页打开别人的项目，然后fork到自己的仓库中去
2. 再用自己仓库里对应项目的地址克隆到本地
3. 修改完成后，把在本地把所做的修改push推送到自己的github上
4. 再用网页打开自己的github项目，找到项目对应的new pull request按钮就可以推送给原作者了
5. 最后如果原作者接受的话，你的修改就会被合并到原作者的项目里