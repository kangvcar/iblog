---
title: "手把手教你用1行Python代码实现FTP服务器 -- Pyftpdlib"
date: "2019-10-15"
draft: false
toc: true
images:
tags: 
  - Pyftpdlib
  - practice
---

> 当你想快速共享一个目录的时候，这是特别有用的，只需要1行代码即可实现。
>
>  FTP 服务器，在此之前我都是使用Linux的vsftpd软件包来搭建FTP服务器的，现在发现了利用pyftpdlib可以更加简单的方法即可实现FTP服务器的功能。

## 环境要求

- Python 2.7
- Windows / Linux

## 环境搭建

```shell
pip install pyftpdlib
```

## 一行代码实现FTP服务器

> 通过Python的`-m`选项作为一个简单的独立服务器来运行，当你想快速共享一个目录的时候，这是特别有用的。

在需要共享的目录下执行如下命令即可把当前目录共享出去（匿名登录）

```shell
python -m pyftpdlib
```

至此一个简单的FTP服务器已经搭建完成，访问 `ftp://127.0.0.1:2121` 即可

（默认IP为 `127.0.0.1` 、端口为 `2121` ）

![效果图](http://upload-images.jianshu.io/upload_images/2640591-49c29aee0c1f8966.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 可选参数

- `-i` 指定IP地址（默认为本机的IP地址）
- `-p`  指定端口（默认为`2121`）
- `-w`  写权限（默认为只读）
- `-d`  指定目录 （默认为当前目录）
- `-u`  指定用户名登录
- `-P`  设置登录密码

------

## 简单示例

上述的一行命令已经可以实现一个简单的FTP服务器，但是要搭建一个功能强大完善的FTP服务所涉及到的配置较多，这时需要使用Pyftpdlib提供的 [API](http://pyftpdlib.readthedocs.io/en/latest/api.html) 来编写。如下是一个简单的示例

```python
from pyftpdlib.authorizers import DummyAuthorizer
from pyftpdlib.handlers import FTPHandler
from pyftpdlib.servers import FTPServer

# 实例化DummyAuthorizer来创建ftp用户
authorizer = DummyAuthorizer()
# 参数：用户名，密码，目录，权限
authorizer.add_user('user', '12345', '/opt/pyftp/test', perm='elradfmwMT')
# 匿名登录
# authorizer.add_anonymous('/home/nobody')

handler = FTPHandler
handler.authorizer = authorizer

# 参数：IP，端口，handler
server = FTPServer(('192.168.56.100', 21), handler)
server.serve_forever()
```

### perm权限选项

读取权限：

- `"e"` =更改目录（CWD，CDUP命令）
- `"l"` =列表文件（LIST，NLST，STAT，MLSD，MLST，SIZE命令）
- `"r"` =从服务器检索文件（RETR命令）

写入权限：

- `"a"` =将数据追加到现有文件（APPE命令）
- `"d"` =删除文件或目录（DELE，RMD命令）
- `"f"` =重命名文件或目录（RNFR，RNTO命令）
- `"m"` =创建目录（MKD命令）
- `"w"` =将文件存储到服务器（STOR，STOU命令）
- `"M"`=更改文件模式/权限（SITE CHMOD命令）
- `"T"`=更改文件修改时间（SITE MFMT命令）

## 参考

Pyftpdlib文档：http://pyftpdlib.readthedocs.io/en/latest/index.html