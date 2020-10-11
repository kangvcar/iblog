---
title: "MongoDB_Install"
date: "2019-02-13"
draft: false
toc: true
images:
tags: 
  - MongoDB
  - Install
---

# Windows 下安装 MongoDB

## MongoDB 安装包下载

- 点击下载 Windowns 平台： [MongoDB 安装包](https://www.mongodb.com/dr/fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.4.10-signed.msi/download)

- [MongoDB 下载中心](https://www.mongodb.com/download-center#community)

## MongoDB 安装

1. 运行安装包，单击Next

![运行安装包，单击Next](http://upload-images.jianshu.io/upload_images/2640591-09f4501a53499175.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

2. 选择安装路径

![选择安装路径](http://upload-images.jianshu.io/upload_images/2640591-6d2fbad81b8682ea.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

3. 单击Next，完成安装

![单击Next，完成安装](http://upload-images.jianshu.io/upload_images/2640591-130c287bbc41dbcf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

4. 安装完成后，进入到安装目录下的/bin目录，能看到很多exe程序

![安装完成后，进入到安装目录下的/bin目录，能看到很多exe程序](http://upload-images.jianshu.io/upload_images/2640591-b02ae4cf8b813ff6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

5. 然后在`/bin`的同级目录下新建`/data`文件夹(用于存放数据和日志)，在`/data`目录下分别新建两个文件夹`/db`(用于存放数据)和`/logs`(用于存放日志)，然后在/data/logs文件夹下创建`mongo.log`文件

![创建文件夹data、db、logs和文件 mongo.log](http://upload-images.jianshu.io/upload_images/2640591-0ff7d273a21ebda0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

6. 以管理员身份运行`cmd.exe` 来创建系统服务

![以管理员身份运行cmd.exe来创建系统服务](http://upload-images.jianshu.io/upload_images/2640591-abe8e803ca7dfaee.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

7. 在命令行下输入如下命令来把MongoDB安装为系统服务: `mongod --bind_ip 0.0.0.0 --logpath C:\MongoDB\Server\3.4\data\logs\mongo.log  --logappend --dbpath C:\MongoDB\Server\3.4\data\db   --port 27017 --serviceName "MongoDB" --serviceDisplayName "MongoDB" --install`

![把MongoDB安装为系统服务](http://upload-images.jianshu.io/upload_images/2640591-3967fbb13624994b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

8. `Win+R` 打开运行窗口输入 `services.msc` 打开系统服务配置来启动刚刚安装的MongoDB服务

![打开系统服务配置来启动刚刚安装的MongoDB服务](http://upload-images.jianshu.io/upload_images/2640591-8caf94ab3a213f37.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

9. 找到 MongoDB 服务并启动

![找到 MongoDB 服务并启动](http://upload-images.jianshu.io/upload_images/2640591-21f24b63cb4e86a9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

10. 把 MongoDB 程序的安装目录下的bin文件夹(C:\MongoDB\Server\3.4\bin)加入系统环境变量

![配置系统环境变量](http://upload-images.jianshu.io/upload_images/2640591-6b89731d901bf614.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

11. 在 cmd 下输入`mongo` 验证MongoDB 是否正常使用

![验证MongoDB 是否正常使用](http://upload-images.jianshu.io/upload_images/2640591-7e9ddf4476ff6f7a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

12. 安装完成，推荐一个MongoDB 可视化工具  [RoboMongo](https://robomongo.org/download)，效果如下图

![RoboMongo](http://upload-images.jianshu.io/upload_images/2640591-58602be7dc9f5e21.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

## 安装MongoDB常见错误

> 安装到上述步骤8时，即启动MongoDB是提示错误：Windowns 不能在 本地计算机 启动 MongoDB

![Windowns 不能在 本地计算机 启动 MongoDB，错误代码100](http://upload-images.jianshu.io/upload_images/2640591-4543a1ac24e7b843.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

> 解决方法：删除/data/db 目录下的所有文件，然后再重新启动MongoDB服务即可成功

![解决方法](http://upload-images.jianshu.io/upload_images/2640591-25fded3dd9f2e6fa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

---

# Ubuntu17.10 下安装 MongoDB

## apt-get 安装 MongoDB

```shell
# 输入命令 apt-get install mongodb
root@kangvcar-VirtualBox:~# apt-get install mongodb
```

## 验证MongoDB是否正常使用

```shell
# 输入命令 mongo
root@kangvcar-VirtualBox:~# mongo
```

![验证成功](http://upload-images.jianshu.io/upload_images/2640591-22402495f9545d0e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)