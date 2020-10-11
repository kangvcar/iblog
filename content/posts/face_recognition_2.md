---
title: "[face_recognition中文文档] 第2节 安装"
date: 2020-10-11T09:50:34+08:00
draft: false
toc: true
images:
tags: 
  - face_recognition
  - 中文文档
---

# Installation安装

## 稳定的版本

要安装Face Recognition面部识别，请在终端中运行此命令：

```python
$ pip3 install face_recognition
```

这是安装面部识别的首选方法，因为它将始终安装最新的稳定版本。

如果您没有安装[pip](https://pip.pypa.io/)，这个[Python安装指南](http://docs.python-guide.org/en/latest/starting/installation/)可以指导您完成该过程。

## 源码

面部识别的源码可以从[Github仓库](https://github.com/ageitgey/face_recognition)下载。

您可以克隆公共仓库：

```python
$ git clone git://github.com/ageitgey/face_recognition
```

或下载[tarball](https://github.com/ageitgey/face_recognition/tarball/master)：

```python
$ curl -OL https://github.com/ageitgey/face_recognition/tarball/master
```

一旦你有源码的副本，你就可以安装它：

```python
$ python setup.py install
```