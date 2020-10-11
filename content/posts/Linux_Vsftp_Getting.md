---
title: "VSFTP服务器搭建"
date: "2020-03-19"
draft: false
toc: true
images:
tags: 
  - Vsftp
  - Server
---

### 1.关闭SELINUX

```shell
#setenforce 0
#vim /etc/selinux/config
    SELINUX=disabled
```

### 2.配置firewalld 

```shell
# firewall-cmd --permanent --zone=public --add-service=ftp 
# firewall-cmd --reload
```

### 3.安装vsftpd

```shell
# yum -y install vsftpd
# systemctl start vsftpd
```

- 启动vsftpd服务后，vsftpd程序默认允许匿名用户登录（用户名为anonymous或ftp，密码为空），和本地用户登录（用/etc/passwd文件验证）
- 匿名用户登录后的目录为/var/ftp/,匿名用户默认只有下载权限，如需上传权限，推荐在/var/ftp/pub目录下赋予权限，不建议在/var/ftp目录赋予上传权限
- 本地用户登录后的目录为用户的家目录，本地用户拥有对家目录的基本权限（创建，删除，上传，修改）
- 本地用户登录后的安全隐患，本地用户可以随意切换到系统的任何目录下，使得系统完全暴露（可以修改配置文件使用chroot_local_user来限制本地用户只能在家目录下进行操作，具体配置下文介绍）

### 4.配置匿名用户的权限

> 使用 man vsftpd.conf 命令查看各个选项的作用。

```shell
anonymous_enable：是否允许匿名登录，默认：YES

anon_mkdir_write_enable：是否允许匿名用户创建新目录，默认：NO

anon_other_write_enable：是否允许匿名用户执行写入操作，如删除和重命名，默认：NO

anon_upload_enable：是否允许匿名用户上传文件，默认：NO

chown_uploads：如果启用，所有匿名上传文件的所有权将更改为设置chown_username中指定的用户，默认：NO
chown_username=kangvcar

no_anon_password：如果启用，所有匿名用户登录将不提示输入密码直接登入，默认：NO

anon_max_rate:匿名客户端允许的最大数据传输速率（以每秒字节数为单位），默认：0（不限制）

anon_umask：用于设置匿名用户创建文件的umask值，默认：077

- 权限交集，不仅需要修改配置文件是否有相应的权限，还要修改对应文件目录的权限
```

### 5.配置本地用户的权限

```shell
local_enable：控制是否允许本地登录。如果启用，则可以使用/etc/passwd中的普通用户帐户（或PAM配置引用的任何地方）登录。默认：YES

write_enable这控制是否允许任何改变文件系统的FTP命令。这些命令是：STOR，DELE，RNFR，RNTO，MKD，RMD，APPE和SITE。默认：YES

chroot_local_user：如果设置为YES，则在登录后，本地用户（默认情况下）在登录时被禁锢在家目录中。默认：NO

chroot_list_enable：如果激活，您可以提供一个本地用户列表，这些用户在登录时被禁锢在家目录中。 如果chroot_local_user设置为YES，则含义稍有不同。在这种情况下，该列表的用户将不会被禁锢在家目录中。 默认情况下，包含此列表的文件是/etc/vsftpd/chroot_list，但您可以使用chroot_list_file设置覆盖此文件。默认：NO

chroot_list_file: 该选项是包含本地用户列表的文件的名称，这些用户在登录时被禁锢在家目录中。该选项仅在启用选项chroot_list_enable时才有用。如果启用了选项chroot_local_user，该列表的用户将不会被禁锢在家目录中。默认：/etvsftpd.confc/vsftpd.chroot_list

userlist_enable：如果启用，vsftpd将从userlist_file给出的文件名中加载用户名列表。如果用户尝试使用此文件中的名称登录，则在他们被要求输入密码之前，他们将被拒绝。 这可能有助于防止传输明文密码。默认：NO

userlist_deny：如果userlist_enable已激活，则检查此选项。 如果您将此设置设置为NO，则用户将被拒绝登录，除非它们明确列在userlist_file指定的文件中。 当登录被拒绝时，在用户被要求输入密码之前发出拒绝。默认：YES

local_max_rate：本地认证用户允许的最大数据传输速率（以每秒字节数为单位）。默认：0（不限制）

max_clients：如果vsftpd处于独立模式，这是可以连接的最大客户端数量。默认：2000

local_umask：用于设置本地用户创建文件的umask值，默认：077

dirlist_enable：如果设置为NO，则所有目录列表命令将授予拒绝权限。默认：YES

dirmessage_enable：如果启用，FTP服务器的用户可以在第一次进入新目录时显示消息。默认情况下，将扫描目录文件.message，但可以使用配置设置message_file覆盖该目录。默认：NO

download_enable：如果设置为NO，则所有下载请求都会拒绝权限。默认：YES

dual_log_enable：如果启用，则会同时生成两个日志文件，默认情况下为/var/log/xferlog和/var/log/vsftpd.log。前者是wu-ftpd风格传输日志，可用标准工具解析。后者是vsftpd自己的样式日志。默认：NO

log_ftp_protocol：启用后，将记录所有FTP请求和响应，只要xferlog_std_format选项未启用即可，用于调试。默认：NO

hide_ids：如果启用，目录列表中的所有用户和组信息将显示为“ftp”。默认：NO

listen：如果启用，vsftpd将以独立模式运行。默认：NO
```