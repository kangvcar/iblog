---
title: "手把手教你用7行代码实现微信聊天机器人 -- Python wxpy"
date: "2019-08-21"
draft: false
toc: true
images:
tags: 
  - wxpy
  - wechat
  - bot
---

# 环境要求

- Windows / Linux / Mac OS
- Python 3.4-3.6，以及 2.7 版本

# wxpy安装

```python
## 使用国内源安装速度快
pip install -U wxpy -i "https://pypi.doubanio.com/simple/"
```

# 实例

### 让机器人与所有好友聊天

```python
from wxpy import *
# 实例化，并登录微信
bot = Bot(cache_path=True)
# 调用图灵机器人API
tuling = Tuling(api_key='4a0488cdce684468b95591a641f0971d')
@bot.register()
def auto_reply(msg):
	tuling.do_reply(msg)
embed()
```

### 让机器人与指定好友聊天

```python
from wxpy import *
# 实例化，并登录微信
bot = Bot(cache_path=True)
# 查找到要使用机器人来聊天的好友
my_friend = ensure_one(bot.search(u'好友名字'))
# 调用图灵机器人API
tuling = Tuling(api_key='4a0488cdce684468b95591a641f0971d')
# 使用图灵机器人自动与指定好友聊天
@bot.register(my_friend)
def reply_my_friend(msg):
    tuling.do_reply(msg)
embed()
```

# 常见问题

Q: 图灵机器人的api_key 怎么获得？

A: 去 http://www.tuling123.com 官网注册帐号就可以获得 api_key