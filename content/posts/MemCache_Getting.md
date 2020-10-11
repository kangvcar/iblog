---
title: "MemCache_Getting"
date: "2019-02-02"
draft: false
toc: true
images:
tags: 
  - MemCache
  - Getting
---

## MemCache 概述

> MemCache虽然被称为”分布式缓存”，但是MemCache本身完全不具备分布式的功能

Memcache 是一个高性能的分布式内存对象缓存系统，用于动态Web应用以减轻数据库负载。它通过在内存中缓存数据和对象来减少读取数据库的次数，从而提高了网站访问的速度。 MemCaChe是一个存储键值对的HashMap，在内存中对任意的数据（比如字符串、对象等）所使用的key-value存储，数据可以来自数据库调用、API调用，或者页面渲染的结果。MemCache设计理念就是小而强大，它简单的设计促进了快速部署、易于开发并解决面对大规模的数据缓存的许多难题，而所开放的API使得MemCache能用于Java、C/C++/C#、Perl、Python、PHP、Ruby等大部分流行的程序语言。

![MemCache作用流程图](http://upload-images.jianshu.io/upload_images/2640591-704c604b778ee691.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


## MemCache 安装

#### Windows 下安装 MemCache

1. 点击下载 [安装包](https://pan.baidu.com/s/1eSnLNSQ)
2. 解压下载后的文件

 ![解压后的文件内容](http://upload-images.jianshu.io/upload_images/2640591-e2e2881f04b22993.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


3. 使用管理员身份运行cmd 并切换到上一步解压后的目录下, 运行命令 `memcached -d install`  来把memcache安装为系统服务, 运行命令 `memcached -d start` 来启动服务

 ![把memcache安装为系统服务](http://upload-images.jianshu.io/upload_images/2640591-724706284f81ea2a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


 ![启动服务](http://upload-images.jianshu.io/upload_images/2640591-cba107d14db5263c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)



4. 如果成功，则可以在系统服务上看到 memcache 服务, memcache默认在localhost的11211端口上启动了一个实例


 ![在系统服务上查看 memcache 服务](http://upload-images.jianshu.io/upload_images/2640591-9346ee8fb03f91f5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


5. 在cmd 下输入命令 `telnet localhost 11211` 来连接 memcache 实例


 ![输入命令 telnet localhost 11211 来连接 memcache 实例](http://upload-images.jianshu.io/upload_images/2640591-f117bb80e6823631.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


#### Ubuntu 下安装 MemCache

1. 使用命令 `sudo apt-get install -y memcached` 来安装memcache
2. 安装完成后默认会在 localhost:11211 上启动一个默认实例
3. 使用命令 `ps aux | grep memcached` 来查看是否正常运行


 ![使用命令 ps aux | grep memcached 来查看是否正常运行](http://upload-images.jianshu.io/upload_images/2640591-2e69ba273ac552b1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


4. 使用命令 `telnet localhost 11211` 来连接memcache实例


 ![使用命令 `telnet localhost 11211` 来连接memcache实例](http://upload-images.jianshu.io/upload_images/2640591-6334ce24d1742d1e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


## MemCache 常用命令

---

### add

添加数据，将 value(数据值) 存储在指定的 key(键) 中

###### 命令格式

```shell
add key flag expiretime bytes
value
```

- key : 给这个值设置一个名字
- flag : 标志，是一个整数
- expiretime : 有效期，以秒为单位，0表示没有延迟
- bytes : 这是一个需要存储在memcached的数据的长度
- value : 是一个需要存储的数据。数据需要将通过在新的一行后输入

###### 范例

```shell
# 添加数据
add name 0 1000 8
kangvcar
STORED
# 如果bytes设置的长度和输入的value长度不符则出现一下错误
add name 0 1000 8
kangvcarrr
CLIENT_ERROR bad data chunk
```

---

### set

为一个新的或现有的键(key)设置一个值

###### 命令格式

```shell
set key flag expiretime bytes
value
```

**参数含义与 add 命令相同**

###### 范例

```shell
为已存在的key(name)设置一个新的值tom
set name 0 1000 3
tom
STORED
```

---

### replace

替换已存在的 key(键) 的 value(数据值)

###### 命令格式

```shell
replace key flag expiretime bytes
value
```

**参数含义与 add 命令相同**

###### 范例

```shell
replace name 0 1000 8
memcache
STORED
```

---

### append

向已存在 key(键) 的 value(数据值) 后面追加数据

###### 命令格式

```shell
append key flag expiretime bytes
value
```

**参数含义与 add 命令相同**

###### 范例

```shell
# 添加数据123到现有键name的后面
append name 0 1000 3
123
STORED
# 查看键name的值
get name
VALUE name 0 11
memcache123
END
```

---

### prepend

向已存在 key(键) 的 value(数据值) 前面追加数据

###### 命令格式

```shell
prepend key flag expiretime bytes
value
```

**参数含义与 add 命令相同**

###### 范例

```shell
prepend name 0 1000 3
456
STORED
get name
VALUE name 0 14
456memcache123
END
```

---

### gets / cas 

`gets` 获取带有 CAS 令牌的 value(数据值)

`cas ` 执行一个"检查并设置"的操作

###### 命令格式

```shell
gets key
```

```shell
cas key flags exptime bytes unique_cas_token [noreply]
value
```

- unique_cas_token : 通过 gets 命令获取的一个唯一的64位值

###### 范例

```shell
# 缺少一个参数 unique_cas_token 
cas name 0 1000 3
ERROR
# unique_cas_token 不正确
cas name 0 1000 3 2
sam
NOT_FOUND
# 通过 gets 命令获取唯一令牌 ==> 12
gets name
VALUE name 0 8 12
kangvcar
END
# 使用 cas 命令更新数据
cas name 0 1000 3 12
sam
STORED
# 使用 get 命令查看数据是否更新
get name
VALUE name 0 3
sam
END
```

---

### get

获取存储在 key(键) 中的 value(数据值) 

###### 命令格式

`get key`

###### 范例

```shell
# 获取存储在键name的值
get name
VALUE name 0 14
456memcache123
END
```

---

### delete

删除已存在的 key(键)

###### 命令格式

`delete key`

###### 范例

```shell
delete age
DELETED
```

---

### incr / decr

 incr 与 decr 命令用于对已存在的 key(键) 的数字值进行自增或自减操作

###### 命令格式

`incr key increment_value`

`decr key increment_value`

###### 范例

```shell
# 添加数据
add age 0 1000 2
15
STORED
# 对已存在的键(age)数字值进行自增
incr age 5
20
# 获取键age的值
get age
VALUE age 0 2
20
END
```

```shell
# 添加数据
add age 0 1000 2
15
STORED
# 对已存在的键(age)数字值进行自减
incr age 5
10
# 获取键age的值
get age
VALUE age 0 2
10
END
```

---

### flush_all

清理缓存中的所有数据

###### 命令格式

`flush_all [time]`

- time : (可选) 在指定时间后执行清理缓存操作

###### 范例

```shell
flush_all
OK
```

---

### stats / stats slabs / stats sizes / stats items

`stats ` 显示统计信息例如 PID(进程号)、版本号、连接数等

`stats slabs` 显示各个slab的信息，包括chunk的大小、数目、使用情况等

`stats sizes` 显示所有item的大小和个数

`stats items` 显示各个 slab 中 item 的数目和存储时长

###### 命令格式

`stats `  `stats slabs`  `stats sizes`   `stats items`

###### 范例

```shell
stats
STAT pid 19700
STAT uptime 3054540758
STAT time 269361355
STAT version 1.4.4-14-g9c660c0
STAT pointer_size 64
...
```

```shell
stats slabs
STAT 1:chunk_size 96
STAT 1:chunks_per_page 10922
STAT 1:total_pages 1
STAT 1:total_chunks 10922
...
```

```shell
stats sizes
STAT 96 2
END
```

```shell
stats items
STAT items:1:number 2
STAT items:1:age 3054540037
STAT items:1:evicted 0
STAT items:1:evicted_nonzero 0
STAT items:1:evicted_time 0
STAT items:1:outofmemory 0
STAT items:1:tailrepairs 0
END
```