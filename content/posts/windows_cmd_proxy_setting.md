---
title: "Windows下为终端设置代理"
date: 2020-10-15T17:18:14+08:00
draft: true
toc: false
images:
tags: 
  - Windows
  - cmd
  - Windows Terminal
  - proxy
  - PowerShell
---

## Windows下的代理环境

代理软件

- shadowsocks
- v2rayN
- Others

在上述代理软件中设置(查看)代理，示例如下(你的看到的设置可能与这个不同，不用担心)：

```shell
http://127.0.0.1:1080
https://127.0.0.1:1080
sock5://127.0.0.1:1080
```

## 为CMD设置代理(Windows Shell同样的设置)

打开cmd窗口，设置代理变量

```
set HTTP_PROXY=http://127.0.0.1:12333
set HTTPS_PROXY=http://127.0.0.1:12333
```

如果设置了用户名和密码，那么设置如下

```
set HTTP_PROXY=http://proxy.com:port
set HTTP_PROXY_USER=username
set HTTP_PROXY_PASS=password

set HTTPS_PROXY=http://proxy.com:port
set HTTPS_PROXY_USER=username
set HTTPS_PROXY_PASS=password
```

上面命令的作用是设置环境变量，不用担心，这种环境变量只会持续到cmd窗口关闭，不是系统环境变量。

![](https://user-images.githubusercontent.com/52482853/63563758-cdcb5f80-c594-11e9-88f7-503dbe8086ed.png)


## 为PowerShell设置代理

打开PowerShell窗口，设置代理变量

```
$env:http_proxy="http://127.0.0.1:1080"
$env:https_proxy="http://127.0.0.1:1080"
```

上面命令的作用是设置环境变量，不用担心，这种环境变量只会持续到PowerShell窗口关闭，不是系统环境变量。

## 测试

```
curl -vv www.google.com
```

> 注意：Ping是ICMP协议，不是TCP/UDP协议，Ping不走代理，所以不要用Ping进行测试。

## 参考链接

- https://github.com/shadowsocks/shadowsocks-windows/issues/1489
- https://gist.github.com/famousgarkin/c5138b1e13ac41920d22
- https://gist.github.com/dreamlu/cf7cbc0b8329ac145fa44342d6a1c01d