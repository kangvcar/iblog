---
title: "INFO-SPIDER —— 集众多数据源于一身的爬虫工具箱"
date: "2020-09-10"
draft: false
toc: true
images:
tags: 
  - INFO-SPIDER
  - Introduction
---

### 场景一

小明一如往常打开 Chrome 浏览器逛着论坛，贴吧，一不小心点开了网页上的广告，跳转到了京东商城，下意识去关闭窗口时发现 （**OS：咦？京东怎么知道我最近心心念念的宝贝呢？刚好我正需要呢！**），既然打开了那就看看商品详情吧 （**OS：哎哟不错哦**），那就下单试试吧！

### 场景二

小白听着网易云音乐的每日推荐歌单无法自拔 （**OS：哇！怎么播放列表里都是我喜欢的音乐风格？网易云音乐太棒了吧!深得我心啊！黑胶会员必须来一个！**），逛着知乎里的“如何优雅的XXX?”，“XXX是怎样一种体验？”，“如何评价XXX?” （**OS：咦？这个问题就是我刚好想问的，原来早已有人提问！什么？？？还有几千条回答！！进去逛逛看！**）

### 场景三

小达上班时不忘充实自己，逛着各大技术论坛博客园、CSDN、开源中国、简书、掘金等等，发现首页的内容推荐太棒了（**OS：这些技术博文太棒了，不用找就出来了**），再打开自己的博客主页发现不知不觉地自己也坚持写博文也有三年了，自己的技术栈也越来越丰富（**OS：怎么博客后台都不提供一个数据分析系统呢？我想看看我这几年来的发文数量，发文时间，想知道哪些博文比较热门，想看看我在哪些技术上花费的时间更多，想看看我过去的创作高峰期时在晚上呢？还是凌晨？我希望系统能给我更多指引数据让我更好的创作！**）

看到以上几个场景你可能会感叹科技在进步，技术在发展，极大地改善了我们的生活方式。

但当你深入思考，你浏览的每个网站，注册的每个网站，他们都记录着你的信息你的足迹。

细思恐极的背后是自己的个人数据被赤裸裸的暴露在互联网上并且被众多的公司利用用户数据获得巨额利益，如对用户的数据收集分析后进行定制的广告推送，收取高额广告费。但作为数据的生产者却没能分享属于自己的数据收益。

### 想法

如果有一个这样的工具，它能帮你拿回你的个人信息，它能帮你把分散在各种站点的个人信息聚合起来，它能帮你分析你的个人数据并给你提供建议，它能帮你把个人数据可视化让你更清楚地了解自己。

> 你是否会需要这样的工具呢? 你是否会喜欢这样的工具呢？

### 实现

![](https://upload-images.jianshu.io/upload_images/2640591-f3f9b729b04efe13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

基于以上，我最近着手开发了 **[INFO-SPIDER](https://github.com/kangvcar/InfoSpider "项目主页")** 

 **INFO-SPIDER** 是一个集众多数据源于一身的爬虫工具箱，旨在安全快捷的帮助用户拿回自己的数据，工具箱代码开源，流程透明。并提供数据分析功能，基于用户数据生成图表文件，使得用户更直观、深入了解自己的信息。 
 
目前支持数据源包括GitHub、QQ邮箱、网易邮箱、阿里邮箱、新浪邮箱、Hotmail邮箱、Outlook邮箱、京东、淘宝、支付宝、中国移动、中国联通、中国电信、知乎、哔哩哔哩、网易云音乐、QQ好友、QQ群、生成朋友圈相册、浏览器浏览历史、12306、博客园、CSDN博客、开源中国博客、简书等等。

### Features

- 安全可靠：本项目为开源项目，代码简洁，所有源码可见，本地运行，安全可靠。
- 使用简单：提供 GUI 界面，只需点击所需获取的数据源并根据提示操作即可。
- 结构清晰：本项目的所有数据源相互独立，可移植性高，所有爬虫脚本在项目的[Spiders](https://github.com/kangvcar/InfoSpider/tree/master/Spiders "Spiders目录")文件下。
- 数据源丰富：本项目目前支持多达24+个数据源，持续更新。
- 数据格式统一：爬取的所有数据都将存储为json格式。
- 个人数据丰富：本项目将尽可能多地为你爬取[个人数据](https://infospider.vercel.app/#/?id=%e6%95%b0%e6%8d%ae%e8%af%b4%e6%98%8e "数据示例")，后期数据处理可根据需要删减。
- 数据分析：本项目提供个人数据的可视化分析，目前仅部分[支持](https://infospider.vercel.app/#/?id=%e6%95%b0%e6%8d%ae%e5%88%86%e6%9e%90 "数据分析")。
- 文档丰富：本项目包含完整全面的[使用说明文档](https://infospider.vercel.app/ "使用说明文档")和[视频教程](https://www.bilibili.com/video/BV14f4y1R7oF/ "视频教程")

### Screenshot

![程序主界面](https://upload-images.jianshu.io/upload_images/2640591-f148e5a116bad6bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![使用说明文档](https://upload-images.jianshu.io/upload_images/2640591-47c3e21c8951292e.gif?imageMogr2/auto-orient/strip)

### 写在最后

1. 该项目解决了个人数据分散在各种各样的公司之间，经常形成数据孤岛，多维数据无法融合的痛点。
2. 作者认为该项目的最大潜力在于能把多维数据进行融合并对个人数据进行分析，是个人数据效益最大化。
3. 该项目使用爬虫手段获取数据，所以程序存在时效问题（需要持续维护，根据网站的更新做出修改）。
4. 该项目的结构清晰，所有数据源相互独立，可移植性高，所有爬虫脚本在项目的[Spiders](https://github.com/kangvcar/InfoSpider/tree/master/Spiders "Spiders目录")文件下，可移植到你的程序中。
5. 目前该项目v1.0版本仅在Windows平台上测试，Python 3.7，未适配多平台。
6. 计划在v2.0版本对项目进行重构，提供web端操作与数据可视化，以适配多平台。
7. 本项目[INFO-SPIDER](https://github.com/kangvcar/InfoSpider "项目主页")代码已开源，欢迎 star 支持。

### 相关链接

- 项目代码：[https://github.com/kangvcar/InfoSpider](https://github.com/kangvcar/InfoSpider)
- 项目使用文档： [https://infospider.vercel.app](https://infospider.vercel.app)
- 项目视频演示： [https://www.bilibili.com/video/BV14f4y1R7oF/](https://www.bilibili.com/video/BV14f4y1R7oF/)
