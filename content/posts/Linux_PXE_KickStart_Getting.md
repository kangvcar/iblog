---
title: "PXE/KickStart 无人值守安装"
date: "2020-03-24"
draft: false
toc: true
images:
tags: 
  - PXE
  - KickStart
  - Server
  - DevOps
---

# 导言

作为中小公司的运维，经常会遇到一些机械式的重复工作，例如：有时公司同时上线几十甚至上百台服务器，而且需要我们在短时间内完成系统安装。

常规的办法有什么？ _光盘安装系统 ===> 一个服务器 DVD 内置光驱百千块，百台服务器都配光驱就浪费了，因为一台服务器也就开始装系统能用的上，以后用的机会屈指可数。用 USB 外置光驱，插来插去也醉了。_ U 盘安装系统 ===>还是同样的问题，要一台一台服务器插 U 盘。 * 网络安装系统 (ftp,http,nfs) ===> 这个方法不错，只要服务器能联网就可以装系统了，但还是需要一台台服务器去敲键盘点鼠标。时刻想偷懒的我们，有没有更好的方法！

高逼格的方法： _Kickstart_ Cobbler

> 在进入主题前，首先会向大家介绍一下什么是 PXE，PXE 能干什么，Kickstart 是什么，Cobbler 又有什么特别。

# 1. 简介

## 1.1 什么是 PXE

- PXE，全名 Pre-boot Execution Environment，预启动执行环境；
- 通过网络接口启动计算机，不依赖本地存储设备（如硬盘）或本地已安装的操作系统；
- 由 Intel 和 Systemsoft 公司于 1999 年 9 月 20 日公布的技术；
- Client/Server 的工作模式；
- PXE 客户端会调用网际协议 (IP)、用户数据报协议(UDP)、动态主机设定协议(DHCP)、小型文件传输协议(TFTP) 等网络协议；
- PXE 客户端 (client) 这个术语是指机器在 PXE 启动过程中的角色。一个 PXE 客户端可以是一台服务器、笔记本电脑或者其他装有 PXE 启动代码的机器（我们电脑的网卡）。

## 1.2 PXE 的工作过程

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_30b7c437-be0d-4523-9444-e39fd94e2c50.jpg)

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_bb6ace67-f0c4-4990-b07c-342678847156.jpg)

1. PXE Client 向 DHCP 发送请求 PXE Client 从自己的 PXE 网卡启动，通过 PXE BootROM(自启动芯片) 会以 UDP(简单用户数据报协议) 发送一个广播请求，向本网络中的 DHCP 服务器索取 IP。

2. DHCP 服务器提供信息 DHCP 服务器收到客户端的请求，验证是否来至合法的 PXE Client 的请求，验证通过它将给客户端一个 “提供” 响应，这个 “提供” 响应中包含了为客户端分配的 IP 地址、pxelinux 启动程序 (TFTP) 位置，以及配置文件所在位置。

3. PXE 客户端请求下载启动文件 客户端收到服务器的 “回应” 后，会回应一个帧，以请求传送启动所需文件。这些启动文件包括：pxelinux.0、pxelinux.cfg/default、vmlinuz、initrd.img 等文件。

4. Boot Server 响应客户端请求并传送文件 当服务器收到客户端的请求后，他们之间之后将有更多的信息在客户端与服务器之间作应答, 用以决定启动参数。BootROM 由 TFTP 通讯协议从 Boot Server 下载启动安装程序所必须的文件 (pxelinux.0、pxelinux.cfg/default)。default 文件下载完成后，会根据该文件中定义的引导顺序，启动 Linux 安装程序的引导内核。

5. 请求下载自动应答文件 客户端通过 pxelinux.cfg/default 文件成功的引导 Linux 安装内核后，安装程序首先必须确定你通过什么安装介质来安装 linux，如果是通过网络安装 (NFS, FTP, HTTP)，则会在这个时候初始化网络，并定位安装源位置。接着会读取 default 文件中指定的自动应答文件 ks.cfg 所在位置，根据该位置请求下载该文件。

    > 这里有个问题，在第 2 步和第 5 步初始化 2 次网络了，这是由于 PXE 获取的是安装用的内核以及安装程序等，而安装程序要获取的是安装系统所需的二进制包以及配置文件。因此 PXE 模块和安装程序是相对独立的，PXE 的网络配置并不能传递给安装程序，从而进行两次获取 IP 地址过程，但 IP 地址在 DHCP 的租期内是一样的。

6. 客户端安装操作系统 将 ks.cfg 文件下载回来后，通过该文件找到 OS Server，并按照该文件的配置请求下载安装过程需要的软件包。 OS Server 和客户端建立连接后，将开始传输软件包，客户端将开始安装操作系统。安装完成后，将提示重新引导计算机。

## 1.3 批量装机软件介绍

Redhat 系主要有两种 Kickstart 和 Cobbler。

Kickstart 是一种无人值守的安装方式。它的工作原理是在安装过程中记录人工干预填写的各种参数，并生成一个名为 ks.cfg 的文件。如果在自动安装过程中出现要填写参数的情况，安装程序首先会去查找 ks.cfg 文件，如果找到合适的参数，就采用所找到的参数；如果没有找到合适的参数，便会弹出对话框让安装者手工填写。所以，如果 ks.cfg 文件涵盖了安装过程中所有需要填写的参数，那么安装者完全可以只告诉安装程序从何处下载 ks.cfg 文件，然后就去忙自己的事情。等安装完毕，安装程序会根据 ks.cfg 中的设置重启 / 关闭系统，并结束安装。

Cobbler 集中和简化了通过网络安装操作系统需要使用到的 DHCP、TFTP 和 DNS 服务的配置。Cobbler 不仅有一个命令行界面，还提供了一个 Web 界面，大大降低了使用者的入门水平。Cobbler 内置了一个轻量级配置管理系统，但它也支持和其它配置管理系统集成，如 Puppet，暂时不支持 SaltStack。

**简单的说，Cobbler 是对 kickstart 的封装，简化安装步骤、使用流程，降低使用者的门槛。**

## 1.4 系统环境准备

```shell
[root@linux-node1 ~]# cat /etc/redhat-release
CentOS release 6.7 (Final)
[root@linux-node1 ~]# uname -r
2.6.32-573.el6.x86_64
[root@linux-node1 ~]# getenforce
Disabled
[root@linux-node1 ~]# /etc/init.d/iptables status
iptables: Firewall is not running.
[root@linux-node1 ~]# ifconfig eth0|awk -F "[ :]+" 'NR==2 {print $4}'
10.0.0.7
[root@linux-node1 ~]# hostname
linux-node1.example.com
```

> 注意： _虚拟机网卡采用 NAT 模式，不要使用桥接模式，因为稍后我们会搭建 DHCP 服务器，在同一局域网多个 DHCP 服务会有冲突。_ VMware 的 NAT 模式的 dhcp 服务也关闭，避免干扰。 ![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_7dc9642d-3d7c-48bb-b122-d56b30f543d8.png)

# 2. 安装 DHCP 服务

## 2.1 DHCP 简介

DHCP（Dynamic Host Configuration Protocol，动态主机配置协议）通常被应用在大型的局域网络环境中，主要作用是集中的管理、分配 IP 地址，使网络环境中的主机动态的获得 IP 地址、网关地址、DNS 服务器地址等信息，并能够提升地址的使用率。

**参考文档：[DHCP 工作过程的六个主要步骤](http://www.zyops.com/dhcp-working-procedure/)**

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_550ad390-3177-4cfa-8cf1-e4b91e8334ce.jpg)

## 2.2 DHCP 服务安装配置

```shell
[root@linux-node1 ~]# yum -y install dhcp
[root@linux-node1 ~]# rpm -ql dhcp |grep "dhcpd.conf"
/etc/dhcp/dhcpd.conf   # 查看配置文件位置
/usr/share/doc/dhcp-4.1.1/dhcpd-conf-to-ldap
/usr/share/doc/dhcp-4.1.1/dhcpd.conf.sample
/usr/share/man/man5/dhcpd.conf.5.gz
[root@linux-node1 ~]# cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp*/dhcpd.conf.sample
#   see 'man 5 dhcpd.conf'
#
[root@linux-node1 ~]# vim /etc/dhcp/dhcpd.conf
subnet 10.0.0.0 netmask 255.255.255.0 {
        range 10.0.0.100 10.0.0.200;
        option subnet-mask 255.255.255.0;
        default-lease-time 21600;
        max-lease-time 43200;
        next-server 10.0.0.7;
        filename "/pxelinux.0";
}
# 注释
range 10.0.0.100 10.0.0.200;         # 可分配的起始IP-结束IP
option subnet-mask 255.255.255.0;    # 设定netmask
default-lease-time 21600;            # 设置默认的IP租用期限
max-lease-time 43200;                # 设置最大的IP租用期限
next-server 10.0.0.7;                # 告知客户端TFTP服务器的ip
filename "/pxelinux.0";              # 告知客户端从TFTP根目录下载pxelinux.0文件
[root@linux-node1 ~]# /etc/init.d/dhcpd start
Starting dhcpd:                                            [  OK  ]
[root@linux-node1 ~]# netstat -tunlp|grep dhcp
udp        0      0 0.0.0.0:67                  0.0.0.0:*                               1573/dhcpd
```

> 本来软件装完后都要加入开机自启动，但这个 Kickstart 系统就不能开机自启动，而且用完后服务都要关闭，防止未来重启服务器自动重装系统了。

> 如果机器数量过多的话，注意 dhcp 服务器的地址池，不要因为耗尽 IP 而导致 dhcpd 服务器没有 IP 地址 release 的情况。

## 2.3 DHCP 指定监听网卡

> 说明：此知识点与本文无关，只是作者用过这个功能，记于此。 **多网卡默认监听 eth0，指定 DHCP 监听 eth1 网卡**

```shell
[root@linux-node1 ~]# vim /etc/sysconfig/dhcpd
# Command line options here
DHCPDARGS=eth1  # 指定监听网卡
[root@linux-node1 ~]# /etc/init.d/dhcpd restart
[root@linux-node1 ~]# tailf /var/log/messages
May 26 14:24:38 Kickstart kernel: e1000: eth1 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: None
May 26 14:29:04 Kickstart dhcpd: Internet Systems Consortium DHCP Server 4.1.1-P1
May 26 14:29:04 Kickstart dhcpd: Copyright 2004-2010 Internet Systems Consortium.
May 26 14:29:04 Kickstart dhcpd: All rights reserved.
May 26 14:29:04 Kickstart dhcpd: For info, please visit https://www.isc.org/software/dhcp/
May 26 14:29:04 Kickstart dhcpd: Not searching LDAP since ldap-server, ldap-port and ldap-base-dn were not specified in the config file
May 26 14:29:04 Kickstart dhcpd: Wrote 0 leases to leases file.
May 26 14:29:04 Kickstart dhcpd: Listening on LPF/eth1/00:0c:29:ea:c1:83/10.0.10.0/24
May 26 14:29:04 Kickstart dhcpd: Sending on   LPF/eth1/00:0c:29:ea:c1:83/10.0.10.0/24
May 26 14:29:04 Kickstart dhcpd: Sending on   Socket/fallback/fallback-net
……
```

# 3. 安装 TFTP 服务

## 3.1 TFTP 简介

TFTP（Trivial File Transfer Protocol, 简单文件传输协议）是 TCP/IP 协议族中的一个用来在客户机与服务器之间进行简单文件传输的协议，提供不复杂、开销不大的文件传输服务。端口号为 69。

## 3.2 TFTP 安装配置

```shell
[root@linux-node1 ~]# yum -y install tftp-server
[root@linux-node1 ~]# vim /etc/xinetd.d/tftp
# default: off
# description: The tftp server serves files using the trivial file transfer \
#       protocol.  The tftp protocol is often used to boot diskless \
#       workstations, download configuration files to network-aware printers, \
#       and to start the installation process for some operating systems.
service tftp
{
        socket_type             = dgram
        protocol                = udp
        wait                    = yes
        user                    = root
        server                  = /usr/sbin/in.tftpd
        server_args             = -s /var/lib/tftpboot # 指定目录，保持默认，不用修改
        disable                 = no # 由原来的yes改为no
        per_source              = 11
        cps                     = 100 2
        flags                   = IPv4
}
[root@linux-node1 ~]# /etc/init.d/xinetd restart
Stopping xinetd:                                           [FAILED]
Starting xinetd:                                           [  OK  ]
[root@linux-node1 ~]# netstat -tunlp|grep 69
udp        0      0 0.0.0.0:69                  0.0.0.0:*                               1106/xinetd
```

# 4. 配置 HTTP 服务

可以用 Apache 或 Nginx 提供 HTTP 服务。Python 的命令 web 服务不行，会有报错。

```shell
[root@linux-node1 ~]# yum -y install httpd
[root@linux-node1 ~]# sed -i "277i ServerName 127.0.0.1:80" /etc/httpd/conf/httpd.conf
[root@linux-node1 ~]# /etc/init.d/httpd start
[root@linux-node1 ~]# mkdir /var/www/html/CentOS-6.7
[root@linux-node1 ~]# mount /dev/cdrom /var/www/html/CentOS-6.7/
mount: block device /dev/sr0 is write-protected, mounting read-only
[root@linux-node1 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        19G  2.4G   16G  14% /
tmpfs           491M   16K  491M   1% /dev/shm
/dev/sda1       190M   36M  145M  20% /boot
/dev/sr0        3.7G  3.7G     0 100% /var/www/html/CentOS-6.7
# 不管怎么弄，只要把安装光盘内容能通过web发布即可。因为是演示，如果复制镜像就有点浪费时间。但生产环境就一定要复制了，光盘读取速度有限。
```

浏览器访问`http://10.0.0.7/CentOS-6.7/`检验配置是否正确。

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_cb337c7b-d104-4e38-b4e8-bd2d11e4cfc8.png)

# 5. 配置支持 PXE 的启动程序

## 5.1 PXE 引导配置（bootstrap）

> syslinux 是一个功能强大的引导加载程序，而且兼容各种介质。SYSLINUX 是一个小型的 Linux 操作系统，它的目的是简化首次安装 Linux 的时间，并建立修护或其它特殊用途的启动盘。如果没有找到 pxelinux.0 这个文件, 可以安装一下。

```shell
[root@linux-node1 ~]# yum -y install syslinux
[root@linux-node1 ~]# cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
# 复制启动菜单程序文件
[root@linux-node1 ~]# cp -a /var/www/html/CentOS-6.7/isolinux/* /var/lib/tftpboot/
[root@linux-node1 ~]# ls /var/lib/tftpboot/
boot.cat  grub.conf   isolinux.bin  memtest     splash.jpg  vesamenu.c32
boot.msg  initrd.img  isolinux.cfg  pxelinux.0  TRANS.TBL   vmlinuz
# 新建一个pxelinux.cfg目录，存放客户端的配置文件。
[root@linux-node1 ~]# mkdir -p /var/lib/tftpboot/pxelinux.cfg
[root@linux-node1 ~]# cp /var/www/html/CentOS-6.7/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
```

## 5.2 PXE 配置文件 default 解析

> 配合虚拟机演示讲解 default 文件。新建一个虚拟机，注意内存需要给 1G。

打开电源后，显示如下

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_QQ_u622A_u56FE20151130185734.jpg)

接下来的画面就是我们熟悉的界面，输入 ESC 可以进入命令行界面。

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_QQ_u622A_u56FE20151130185746.jpg)

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_0a1a06b4-2f99-4574-828a-8ced75cdf5f8.png)

```shell
[root@linux-node1 ~]# vim /var/lib/tftpboot/pxelinux.cfg/default
default vesamenu.c32  # 默认加载一个菜单
#prompt 1             # 开启会显示命令行'boot: '提示符。prompt值为0时则不提示，将会直接启动'default'参数中指定的内容。
timeout 600           # timeout时间是引导时等待用户手动选择的时间，设为1可直接引导，单位为1/10秒。

display boot.msg
# 菜单背景图片、标题、颜色。
menu background splash.jpg
menu title Welcome to CentOS 6.7!
menu color border 0 #ffffffff #00000000
menu color sel 7 #ffffffff #ff000000
menu color title 0 #ffffffff #00000000
menu color tabmsg 0 #ffffffff #00000000
menu color unsel 0 #ffffffff #00000000
menu color hotsel 0 #ff000000 #ffffffff
menu color hotkey 7 #ffffffff #ff000000
menu color scrollbar 0 #ffffffff #00000000

# label指定在boot:提示符下输入的关键字，比如boot:linux[ENTER]，这个会启动label linux下标记的kernel和initrd.img文件。
label linux       # 一个标签就是前面图片的一行选项。
  menu label ^Install or upgrade an existing system
  menu default
  kernel vmlinuz  # 指定要启动的内核。同样要注意路径，默认是/tftpboot目录。
  append initrd=initrd.img # 指定追加给内核的参数，initrd.img是一个最小的linux系统
label vesa
  menu label Install system with ^basic video driver
  kernel vmlinuz
  append initrd=initrd.img nomodeset
label rescue
  menu label ^Rescue installed system
  kernel vmlinuz
  append initrd=initrd.img rescue
label local
  menu label Boot from ^local drive
  localboot 0xffff
label memtest86
  menu label ^Memory test
  kernel memtest
  append -
```

# 6. 预热之手动网络安装

1、新建一台空白虚拟机，也不要挂载 ISO 镜像，打开电源。

扩展：DEll R710 服务器开机后，根据提示按 F12，进行 PXE 启动。物理服务器有 4 个网卡，所以还会让选择从哪个网卡启动。

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_157d7923-e153-4929-8b30-05264f27ba8e.jpg)

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_fafcf419-7d49-4225-a085-8a5922a7e040.png)

2、选择第一个选项

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_d105483f-e359-48fc-aad6-454459660d83.jpg)

3、选择英语

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_87b790d2-e5af-4619-8079-aa46aad7fae4.png)

4、选择美式键盘

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_3368e717-c343-4dfe-a836-78be31ec9e8d.png)

5、选择 URL 方式安装

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_338a90fc-ca29-4c3b-a239-91bc33311799.png)

> 注：使用 NFS 的写法 nfs:10.0.0.1:/data/sys/kickstart/ks.cfg

6、禁用 IPv6

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_6ccc17f5-0d73-4b8e-82d3-1214ad167582.png)

7、输入 http 的地址

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_aa1efc26-5605-4e01-b7d4-5ec1f0837f0c.png)

8、接下来和光盘安装一样

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_f3b00f18-b71f-48bb-8d35-a46eed98f2f3.jpg)

# 7. 创建 ks.cfg 文件

通常，我们在安装操作系统的过程中，需要大量的和服务器交互操作，为了减少这个交互过程，kickstart 就诞生了。使用这种 kickstart，只需事先定义好一个 Kickstart 自动应答配置文件 ks.cfg（通常存放在安装服务器上），并让安装程序知道该配置文件的位置，在安装过程中安装程序就可以自己从该文件中读取安装配置，这样就避免了在安装过程中多次的人机交互，从而实现无人值守的自动化安装。

生成 kickstart 配置文件的三种方法：
- 方法1：每安装好一台 Centos 机器，Centos 安装程序都会创建一个 kickstart 配置文件，记录你的真实安装配置。如果你希望实现和某系统类似的安装，可以基于该系统的 kickstart 配置文件来生成你自己的 kickstart 配置文件。(生成的文件名字叫 anaconda-ks.cfg 位于 /root/anaconda-ks.cfg)
- 方法2：Centos 提供了一个图形化的 kickstart 配置工具。在任何一个安装好的 Linux 系统上运行该工具，就可以很容易地创建你自己的 kickstart 配置文件。kickstart 配置工具命令为 redhat-config-kickstart（RHEL3）或 system-config-kickstart（RHEL4，RHEL5）. 网上有很多用 CentOS 桌面版生成 ks 文件的文章，如果有现成的系统就没什么可说。但没有现成的，也没有必要去用桌面版，命令行也很简单。 
- 方法3：阅读 kickstart 配置文件的手册。用任何一个文本编辑器都可以创建你自己的 kickstart 配置文件。

## 7.1 查看 anaconda-ks.cfg

```shell
[root@linux-node1 ~]# cat anaconda-ks.cfg
# Kickstart file automatically generated by anaconda.

#version=DEVEL
install
cdrom
lang en_US.UTF-8
keyboard us
network --onboot yes --device eth0 --bootproto static --ip 10.0.0.6 --netmask 255.255.255.0 --gateway 10.0.0.2 --noipv6 --nameserver 10.0.0.2 --hostname centos67
rootpw  --iscrypted $6$2Frfm3Sc/oWdF2Yb$GTS8VbsaeLzAfT46EYJWezKA7VMOnefDAH6anyb12Hu5K1qu1awlpTwBxTjAzXpV/.l983Irdwxo5Rks0QV1p1
firewall --service=ssh
authconfig --enableshadow --passalgo=sha512
selinux --enforcing
timezone --utc Asia/Shanghai
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet"
# The following is the partition information you requested
# Note that any partitions you deleted are not expressed
# here so unless you clear all partitions first, this is
# not guaranteed to work
#clearpart --none

#part /boot --fstype=ext4 --asprimary --size=200
#part swap --asprimary --size=1024
#part / --fstype=ext4 --grow --asprimary --size=200

repo --  --baseurl=cdrom:sr0 --cost=100

%packages
@base
@compat-libraries
@core
@debugging
@development
@server-policy
@workstation-policy
python-dmidecode
sgpio
device-mapper-persistent-data
systemtap-client
```

## 7.2 ks.cfg 详解

官网文档

- CentOS5 : [http://www.centos.org/docs/5/html/Installation_Guide-en-US/s1-kickstart2-options.html](http://www.centos.org/docs/5/html/Installation_Guide-en-US/s1-kickstart2-options.html)

- CentOS6 : [https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/s1-kickstart2-options.html](https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/s1-kickstart2-options.html) 

官网自带中文版，选一下语言即可 `ks.cfg`文件组成大致分为 3 段

- 命令段 键盘类型，语言，安装方式等系统的配置，有必选项和可选项，如果缺少某项必选项，安装时会中断并提示用户选择此项的选项

- 软件包段

```shell
%packages
@groupname：指定安装的包组
package_name：指定安装的包
-package_name：指定不安装的包
```

在安装过程中默认安装的软件包，安装软件时会自动分析依赖关系。

- 脚本段 (可选)

```shell
%pre:安装系统前执行的命令或脚本(由于只依赖于启动镜像，支持的命令很少)
%post:安装系统后执行的命令或脚本(基本支持所有命令)
```

| 关键字 | 含义 |
| --- | --- |
| `install` | 告知安装程序，这是一次全新安装，而不是升级`upgrade`。 |
| `url --url=" "` | 通过`FTP`或`HTTP`从远程服务器上的安装树中安装。|
|`url --url="http://10.0.0.7/CentOS-6.7/"`||
|`url --url ftp://<username>:<password>@<server>/<dir>` ||
| `nfs` | 从指定的`NFS`服务器安装。|
|`nfs --server=nfsserver.example.com --dir=/tmp/install-tree` ||
| `text` | 使用文本模式安装。 |
| `lang` | 设置在安装过程中使用的语言以及系统的缺省语言。`lang en_US.UTF-8` |
| `keyboard` | 设置系统键盘类型。`keyboard us` |
| `zerombr` | 清除`mbr`引导信息。 |
| `bootloader` | 系统引导相关配置。|
|`bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet"`||
|`--location=`|,指定引导记录被写入的位置. 有效的值如下:`mbr`(缺省),`partition`(在包含内核的分区的第一个扇区安装引导装载程序) 或`none`(不安装引导装载程序)。|
|`--driveorder`| 指定在`BIOS`引导顺序中居首的驱动器。|
|`--append=`| 指定内核参数. 要指定多个参数, 使用空格分隔它们。 |
| `network` | 为通过网络的`kickstart`安装以及所安装的系统配置联网信息。|
|`network --bootproto=dhcp --device=eth0 --onboot=yes --noipv6 --hostname=CentOS6`||
|`--bootproto=[dhcp/bootp/static]`中的一种，缺省值是`dhcp`。|`bootp`和`dhcp`被认为是相同的。`static`方法要求在`kickstart`文件里输入所有的网络信息。|
|`network --bootproto=static --ip=10.0.0.100 --netmask=255.255.255.0 --gateway=10.0.0.2 --nameserver=10.0.0.2`|请注意所有配置信息都必须在一行上指定, 不能使用反斜线来换行。|
|`--ip=`| 要安装的机器的`IP`地址.|
|`--gateway=`|IP 地址格式的默认网关.|
|`--netmask=`| 安装的系统的子网掩码.|
|`--hostname=`| 安装的系统的主机名.|
|`--onboot=`| 是否在引导时启用该设备.|
|`--noipv6=`| 禁用此设备的`IPv6`.|
|`--nameserver=`| 配置`dns`解析. |
| `timezone` | 设置系统时区。`timezone --utc Asia/Shanghai` |
| `authconfig` | 系统认证信息。|
|`authconfig --enableshadow --passalgo=sha512`|设置密码加密方式为`sha512` 启用`shadow`文件。 |
| `rootpw` | `root`密码 |
| `clearpart` | 清空分区。|
|`clearpart --all --initlabel`|`--all` 从系统中清除所有分区，`--initlable` 初始化磁盘标签 |
| `part` | 磁盘分区。|
|`part /boot --fstype=ext4 --asprimary --size=200`||
|`part swap --size=1024`||
|`part / --fstype=ext4 --grow --asprimary --size=200`||
|`--fstype=`| 为分区设置文件系统类型. 有效的类型为`ext2`,`ext3`,`swap`和`vfat`。|
|`--asprimary`| 强迫把分区分配为主分区, 否则提示分区失败。|
|`--size=`| 以`MB`为单位的分区最小值. 在此处指定一个整数值, 如`500`. 不要在数字后面加`MB`。|
|`--grow`| 告诉分区使用所有可用空间 (若有), 或使用设置的最大值。 |
| `firstboot` | 负责协助配置 redhat 一些重要的信息。|
|`firstboot --disable` ||
| `selinux` | 关闭`selinux`。`selinux --disabled` |
| `firewall` | 关闭防火墙。`firewall --disabled` |
| `logging` | 设置日志级别。`logging --level=info` |
| `reboot` | 设定安装完成后重启, 此选项必须存在，不然 kickstart 显示一条消息，并等待用户按任意键后才重新引导，也可以选择`halt`关机。 |

## 7.3 编写 ks 文件

```shell
# 先生成一个密码备用
[root@linux-node1 ~]# grub-crypt
Password:123456
Retype password:123456
$6$X20eRtuZhkHznTb4$dK0BJByOSAWSDD8jccLVFz0CscijS9ldMWwpoCw/ZEjYw2BTQYGWlgKsn945fFTjRC658UXjuocwJbAjVI5D6/
[root@linux-node1 ~]# mkdir /var/www/html/ks_config
[root@linux-node1 ~]# vim /var/www/html/ks_config/CentOS-6.7-ks.cfg
# Kickstart Configurator for CentOS 6.7 by yao zhang
install
url --url="http://10.0.0.7/CentOS-6.7/"
text
lang en_US.UTF-8
keyboard us
zerombr
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet"
network --bootproto=dhcp --device=eth0 --onboot=yes --noipv6 --hostname=CentOS6
timezone --utc Asia/Shanghai
authconfig --enableshadow --passalgo=sha512
rootpw  --iscrypted $6$X20eRtuZhkHznTb4$dK0BJByOSAWSDD8jccLVFz0CscijS9ldMWwpoCw/ZEjYw2BTQYGWlgKsn945fFTjRC658UXjuocwJbAjVI5D6/
clearpart --all --initlabel
part /boot --fstype=ext4 --asprimary --size=200
part swap --size=1024
part / --fstype=ext4 --grow --asprimary --size=200
firstboot --disable
selinux --disabled
firewall --disabled
logging --level=info
reboot
%packages
@base
@compat-libraries
@debugging
@development
tree
nmap
sysstat
lrzsz
dos2unix
telnet

%post
wget -O /tmp/optimization.sh http://10.0.0.7/ks_config/optimization.sh &>/dev/null
/bin/sh /tmp/optimization.sh
%end
```

## 7.4 开机优化脚本

```shell
[root@linux-node1 ~]# vim /var/www/html/ks_config/optimization.sh
#!/bin/bash
##############################################################
# File Name: /var/www/html/ks_config/optimization.sh
# Version: V1.0
# Author: yao zhang
# Organization: www.zyops.com
# Created Time : 2015-12-03 15:23:08
# Description: Linux system initialization
##############################################################

. /etc/init.d/functions

Ip=10.0.0.7
Port=80
ConfigDir=ks_config

# Judge Http server is ok?
PortNum=`nmap $Ip  -p $Port 2>/dev/null|grep open|wc -l`
[ $PortNum -lt 1 ] && {
        echo "Http server is bad!"
        exit 1
}

# Defined result function
function Msg(){
        if [ $? -eq 0 ];then
          action "$1" /bin/true
        else
          action "$1" /bin/false
        fi
}

# Defined IP function
function ConfigIP(){
        Suffix=`ifconfig eth0|awk -F "[ .]+" 'NR==2 {print $6}'`
        cat >/etc/sysconfig/network-scripts/ifcfg-eth0 <<-END
        DEVICE=eth0
        TYPE=Ethernet
        ONBOOT=yes
        NM_CONTROLLED=yes
        BOOTPROTO=none
        IPADDR=10.0.0.$Suffix
        PREFIX=24
        GATEWAY=10.0.0.2
        DNS1=10.0.0.2
        DEFROUTE=yes
        IPV4_FAILURE_FATAL=yes
        IPV6INIT=no

        END
        Msg "config eth0"
}

# Defined Yum source Functions
function yum(){
        YumDir=/etc/yum.repos.d
        [ -f "$YumDir/CentOS-Base.repo" ] && cp $YumDir/CentOS-Base.repo{,.ori} 
        wget -O $YumDir/CentOS-Base.repo http://$Ip:$Port/$ConfigDir/CentOS-Base.repo &>/dev/null &&\
        wget -O $YumDir/epel.repo http://$Ip:$Port/$ConfigDir/epel.repo &>/dev/null &&\
        Msg "YUM source"
}

# Defined Hide the system version number Functions
function HideVersion(){
        [ -f "/etc/issue" ] && >/etc/issue
        Msg "Hide issue" 
        [ -f "/etc/issue.net" ] && > /etc/issue.net
        Msg "Hide issue.net"
}

# Defined OPEN FILES Functions
function openfiles(){
        [ -f "/etc/security/limits.conf" ] && {
        echo '*  -  nofile  65535' >> /etc/security/limits.conf
        Msg "open files"
        }
}

# Defined Kernel parameters Functions
function kernel(){
        KernelDir=/etc
        [ -f "$KernelDir/sysctl.conf" ] && /bin/mv $KernelDir/sysctl.conf{,.ori}
        wget -O $KernelDir/sysctl.conf http://$Ip:$Port/$ConfigDir/sysctl.conf &>/dev/null
        Msg "Kernel config"
}

# Defined System Startup Services Functions
function boot(){
        for oldboy in `chkconfig --list|grep "3:on"|awk '{print $1}'|grep -vE "crond|network|rsyslog|sshd|sysstat"` 
          do 
           chkconfig $oldboy off
        done
        Msg "BOOT config"
}

# Defined Time Synchronization Functions
function Time(){
        echo "#time sync by zhangyao at $(date +%F)" >>/var/spool/cron/root
        echo '*/5 * * * * /usr/sbin/ntpdate time.nist.gov &>/dev/null' >>/var/spool/cron/root
        Msg "Time Synchronization"
}

# Defined main Functions
function main(){
        ConfigIP
        yum
        HideVersion
        openfiles
        kernel
        boot
        Time
}

main
# rz上传CentOS-Base.repo、epel.repo、sysctl.conf
```

### 7.5 整合编辑 default 配置文件

```shell
# 最精简配置
[root@linux-node1 ~]# vim /var/lib/tftpboot/pxelinux.cfg/default
default ks
prompt 0

label ks
  kernel vmlinuz
  append initrd=initrd.img ks=http://10.0.0.7/ks_config/CentOS-6.7-ks.cfg # 告诉安装程序ks.cfg文件在哪里

# append initrd=initrd.img ks=http://10.0.0.7/ks_config/CentOS-6.7-ks.cfg ksdevice=eth0
# ksdevice=eth0代表当客户端有多块网卡的时候，要实现自动化需要设置从eth1安装，不指定的话，安装的时候系统会让你选择，那就不叫全自动化了。
```

# 8. 无人值守自动安装

打开系统电源，出去喝杯水。过会回来, 系统就以经装好了.^_^ 下面是安装过程中截的几个图

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_61569354-f400-414f-b3f3-6a42a48f1ad0.png)

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_36bd81ad-cc7a-4b61-8aea-21ed5be68ac2.png)

如果安装过程出错，可以通过 Alt+F2-F5 切到其他控制台查看报错信息

# 9. 安装完成后验证

```shell
[root@CentOS6 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2       6.6G  1.4G  4.9G  22% /
tmpfs           491M     0  491M   0% /dev/shm
/dev/sda1       190M   36M  145M  20% /boot
[root@CentOS6 ~]# /etc/init.d/iptables status
iptables：未运行防火墙。
[root@CentOS6 ~]# getenforce
Disabled
[root@CentOS6 ~]# ifconfig eth0|awk -F "[ :]+" 'NR==2 {print $4}'
10.0.0.101
[root@CentOS6 ~]# chkconfig --list|grep "3:on"
crond           0:off   1:off   2:on    3:on    4:on    5:on    6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
rsyslog         0:off   1:off   2:on    3:on    4:on    5:on    6:off
sshd            0:off   1:off   2:on    3:on    4:on    5:on    6:off
sysstat         0:off   1:on    2:on    3:on    4:on    5:on    6:off
```

# 10. 知识扩展

## 10.1 PXE 配置文件 default

由于多个客户端可以从一个 PXE 服务器引导，PXE 引导映像使用了一个复杂的配置文件搜索方式来查找针对客户机的配置文件。如果客户机的网卡的 MAC 地址为`8F：3H：AA：6B：CC：5D`，对应的 IP 地址为 10.0.0.195，那么客户机首先尝试以 MAC 地址为文件名匹配的配置文件，如果不存在就以 IP 地址来查找。根据上述环境针对这台主机要查找的以一个配置文件就是 `/tftpboot/pxelinux.cfg/01-8F：3H：AA：6B：CC：5D`。如果该文件不存在，就会根据 IP 地址来查找配置文件了，这个算法更复杂些，PXE 映像查找会根据 IP 地址 16 进制命名的客户机配置文件。例如：10.0.0.195 对应的 16 进制的形式为 C0A801C3。（可以通过 syslinux 软件包提供的 gethostip 命令将 10 进制的 IP 转换为 16 进制）

如果 C0A801C3 文件不存在，就尝试查找 C0A801C 文件，如果 C0A801C 也不存在，那么就尝试 C0A801 文件，依次类推，直到查找 C 文件，如果 C 也不存在的话，那么最后尝试 default 文件。

总体来说，pxelinux 搜索的文件的顺序是：

```shell
/tftpboot/pxelinux.cfg/01-88-99-aa-bb-cc-dd
/tftpboot/pxelinux.cfg/C0A801C3
/tftpboot/pxelinux.cfg/C0A801C
/tftpboot/pxelinux.cfg/C0A801
/tftpboot/pxelinux.cfg/C0A80
/tftpboot/pxelinux.cfg/C0A8
/tftpboot/pxelinux.cfg/C0A
/tftpboot/pxelinux.cfg/C0
/tftpboot/pxelinux.cfg/C
/tftpboot/pxelinux.cfg/default
```

应用：如果已经从厂商获取了服务器 MAC 地址，就可以差异化定制安装服务器了。

## 10.2 Cobbler

大家学完 kickstart，会发现一个问题，kickstart 不怎么灵活，调整某些参数就需要另外一个 ks.cfg 文件。因此 [Cobbler 无人值守安装](http://www.zyops.com/autoinstall-cobbler/)可以一观。

# 11. 报错

![](http://www.zyops.com/images/wpid-0208446e92da93ca8e75bd0e4e5ec7a4_39ec6f2b-0466-409e-9f41-6c1698dee4e9.jpg)

**报错原因：selinux 没关**