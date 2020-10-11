---
title: "NFS 服务器搭建"
date: "2020-03-20"
draft: false
toc: true
images:
tags: 
  - NFS
  - Server
---

> NFS（网络文件系统）用于Unix/Linux之间的文件共享，NFS在文件传送过程中依赖与RPC（远程过程调用）协议。NFS本身是没有提供信息传送的协议和功能。只要用到NFS的地方都需要启动RPC服务，不论是NFS的服务端还是客户端。

> 在启动NFS服务之前，首先要启动RPC服务（CentOS5是portmap服务，CentOS6.6以后的版本是rpcbind服务），否则NFS服务器无法向RPC服务注册。另外，RPC服务如果重启，原来以及有的NFS端口就会丢失，因此，只要RPC服务重启，NFS服务就要重启向RPC重新注册新的随机端口号。一般修改NFS配置文件后，不需要重启服务，直接平滑重启即可，命令:/etc/init.d/nfs reload或者exportfs -rv就可以修改/etc/exports生效。

> 相关软件包：nfs-utils(NFS服务主程序)、rpcbind(PRC服务程序)

- `nfsd`监听的端口: 2049/tcp, 2049/udp
- `portmapper`监听的端口： 111/tcp, 111/udp
- 相关配置文件：
    - /etc/exports
    - /etc/sysconfig/nfs
    - /etc/nfsmount.conf
    - /etc/nfs.conf

### 1. 安装并启动nfs-utils、rpcbind

CentOS和Ubuntu安装的软件包有差异，下面分别介绍两个系统下的安装方法，其他操作基本没有差异

#### CentOS下安装的软件包为nfs-utils和rpcbind

```shell
yum -y install nfs-utils rpcbind
# 启动顺序很关键，必须先启动rpcbind服务，使得nfs服务可以像rpc服务注册端口
systemctl restart rpcbind
systemctl restart nfs
```

#### Ubuntu下安装的软件包为nfs-kernel-server和rpcbind

```shell
sudo apt install -y nfs-kernel-server rpcbind
#启动顺序很关键，必须先启动rpcbind服务，使得nfs服务可以像rpc服务注册端口
systemctl restart rpcbind
systemctl restart nfs-kernel-server
```

#### 查看NFS服务向rpc注册的端口信息，主端口号是：111

```shell
rpcinfo -p 192.168.10.10
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100005    1   udp  20048  mountd
    100005    1   tcp  20048  mountd
    100005    2   udp  20048  mountd
    100005    2   tcp  20048  mountd
    100005    3   udp  20048  mountd
    100005    3   tcp  20048  mountd
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
    100003    3   udp   2049  nfs
    100003    4   udp   2049  nfs
    100227    3   udp   2049  nfs_acl
    100021    1   udp  37633  nlockmgr
    100021    3   udp  37633  nlockmgr
    100021    4   udp  37633  nlockmgr
    100021    1   tcp  44262  nlockmgr
    100021    3   tcp  44262  nlockmgr
    100021    4   tcp  44262  nlockmgr
```

### 2. 添加共享条目

```shell
# 编辑/etc/exports文件添加共享条目
vim /etc/exports
/shared  192.168.10.0/24(rw,sync,all_squash,anonuid=505,anongid=505) 192.168.20.0/24(rw)

# 修改共享目录权限和属主属组
mkdir /shared
chown -R nfsnobody:nfsnobody /shared
chmod -R 760 /shared

# 重新导出共享目录
exportfs -arv

# 测试
showmount -e 192.168.10.10

Export list for 192.168.10.10:
/shared 192.168.10.0/24
输出如下则表示成功
```

条目格式：(man exports查看man手册)

共享目录  NFS客户端地址(属性1,属性2)

- ro: 只读
- rw: 读写（还需要配置共享目录有读写权限）
- sync: 数据同步写入NFS服务器端的硬盘中
- async: 数据先写到缓存区，再写到磁盘里中
- root_squash: NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，拥有匿名用户权限，通常他将使用nobody或nfsnobody身份
- no_root_squash: NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，也拥有root权限。显然开启这项是不安全的
- all_squash: 不论NFS客户端连接服务端时使用什么用户，对服务端分享的目录来说都是拥有匿名用户权限
- anonuid：指定匿名用户的UID值，通常是nobody或nfsnobody，可以在此处自行设定
- anongid: 指定匿名用户的GID值

### 3. 客户端挂载使用

```shell
# 客户端也要安装nfs-utils、rpcbind
yum -y install nfs-utils rpcbind

# 客户端只需要启动rpcbind服务即可，不需要启动nfs服务
systemctl restart rpcbind

# 列出服务端的共享目录
showmount -e 192.168.10.10

Export list for 192.168.10.10:
/shared 192.168.10.0/24

# 挂载文件系统到本地
mount -t nfs 192.168.10.10:/shared /nfs 
df -h 
192.168.10.10:/shared     17G  5.4G   12G  32% /nfs
```

### 4. 相关命令

#### showmount

显示NFS服务器的挂载信息

```shell
showmount -e NFS_SERVER: 查看NFS服务器“导出”的各文件系统
showmount -a NFS_SERVER: 查看NFS服务器所有被挂载的文件系统及其挂载的客户端对应关系列表
showmount -d NFS_SERVER: 显示NFS服务器所有导出的文件系统中被客户端挂载了文件系统列表
```

#### exportfs

在修改了/etc/exports文件后执行此命令即可重新导出或者取消导出所有文件系统，而不需要重新启动nfs服务

```shell
-a：跟-r或-u选项同时使用，表示重新挂载所有文件系统或取消导出所有文件系统；
-r: 重新导出
-u: 取消导出
-v: 显示详细信息
```

#### rpcinfo

显示RPC信息

```shell
-p：用rpc协议来探测主机host上使用的rpcbind，并显示所有已注册的RPC程序
```

#### nfsstat

查看NFS的运行状态，对于调整NFS的运行有很大帮助

### 5.NFS性能调优 

使用dd命令测试读写性能，并根据读写性能修改配置文件以允许最大nfs客户端连接数

```shell
time dd if=/dev/zero of=/nfs/test bs=10k count=10000

10000+0 records in
10000+0 records out
102400000 bytes (102 MB) copied, 0.604919 s, 169 MB/s

real    0m0.608s
user    0m0.002s
sys     0m0.078s

修改配置文件/etc/sysconfig/nfs中的参数RPCNFSDCOUNT,该参数默认值为8
```

### Q&A

1. 客户端挂载时，报错误`mount clntudp_create: RPC: Port mapper failure - RPC: Unable to receive`

- 通过命令`rpcinfo -p`来查看portmap服务是否正常启动以及相应的端口(默认111)
- 检查防火墙设置，允许tcp，udp的111端口访问
- 检查/etc/hosts.deny，/etc/hosts.allow看客户端连接是否被阻止了

2. 客户端执行命令`showmount -e NFS_SERVER_IP`时，报错误：`mount clntudp_create: RPC: Program not registered`

- nfs或rpcbind服务没有启动，使用chkconfig把nfs、rpcbind加到系统服务中并用service来启动

3. 客户端执行命令`showmount -e NFS_SERVER_IP`时，报错误：`rpc mount export: RPC: Unable to receive; errno = No route to host`

- 配置文件：/etc/sysconfig/nfs找到nfs服务相关端口设置的地方，并移除注释并配置指定端口，然后在iptables防火墙设置中指定允许相应端口的Udp，tcp流通过。

4. 执行`showmount -e NFS_SERVER_IP`成功，但是挂载时报错：`mount: mount to NFS server '192.168.10.10' failed: System Error: No route to host.`

- 这是由于nfs服务的默认端口2049被防火墙阻塞了，修改iptables允许2049端口通过

5. 执行`showmount -e NFS_SERVER_IP`成功，但是挂载时报错：`mount: mount to NFS server '192.168.10.10' failed: timed out (retrying). `

- 编辑iptables相关端口的tcp端口允许通过，而udp不允许。

6. 服务端的/etc/exports配置文件中目录权限属性设置为rw(默认为root_squash)，但是在客户端mount目录执行touch命令时报错误：`touch: cannot touch a: Permission denied`

- 检查服务器端共享目录是否有写入权限 `ll -d /shared`
- 修改服务器端共享目录权限 `chown -R 760 /shared`(文件所有者root有全权限、文件所有组用户有读写权限、其他用户无权限，然后把目录的组设置为nfsnobody)
- 修改服务器端共享目录属主属组为nfsnobody`chown -R nfsnobody:nfsnobody /shared`

### 其他博文

http://server.51cto.com/sManage-150923.htm