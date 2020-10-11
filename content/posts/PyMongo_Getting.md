---
title: "PyMongo_Getting"
date: "2019-02-14"
draft: false
toc: true
images:
tags: 
  - PyMongo
  - Getting
---

# Python 连接 MongoDB

## 安装PyMongo模块

```python
pip install pymongo
```

## 使用MongoClient建立连接

```python
from pymongo import MongoClient
# 以下为三种建立连接的方式
#client = MongoClient()
#client = MongoClient('localhost', 27017)
#client = MongoClient('mongodb://localhost:27017/')
```

## 获取数据库

```python
# 以下是两种获取数据库的方式
db = client.pythondb
db = client['python-db']
```

## 获取集合

```python
# 以下是两种获取集合的方式
collection = db.python_collection
collection = db['python-collection']
```

**上述任何命令都没有在MongoDB服务器上实际执行任何操作。当第一个文档插入集合时才创建集合和数据库。**

## 插入文档

```python
#!/usr/bin/python3
#coding=utf-8

import datetime
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
posts = db.posts

post = {"author": "Maxsu",
         "text": "My first blog post!",
         "tags": ["mongodb", "python", "pymongo"],
         "date": datetime.datetime.utcnow()}

posts.insert(post)

# 批量插入,参数为list
posts.insert_many(new_posts)
```

## 查找文档

```python
#!/usr/bin/python3
#coding=utf-8

import datetime
import pprint
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
posts = db.posts

# 查找单个文档
print(posts.find_one())

# 查找多个文档
for post in posts.find():
    print(post)

# 计数统计
print(posts.count())
print(posts.find({"author": "Maxsu"}).count())
```
