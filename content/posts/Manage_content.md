---
title: "本站搭建过程记录"
date: "2018-01-10"
draft: false
toc: true
images:
tags: 
  - Usage
  - Recode
---

> Tools： Hugo[^1] & GitHub[^2] & Vercel[^3] & Win10

# 为什么选择 Hugo?

- ***免费***
- Go语言实现的静态网站生成器
- 快，简单，易用，高效，扩展性好
- 超详细的文档[^4],活跃的社区
- 更加自由的内容组织方式
- 丰富的主题[^5]
- 快速部署[^3]

# 为什么选择 Vercel?

- ***免费***
- Vercel 是一家提供静态网站托管的平台
- 支持自动从Github等仓库拉取代码
- 支持按自定义构建方式进行构建，最后把生成的静态网站进行发布
- 支持自定义域名，自动申请SSL证书等功能
- 国内访问速度比GitHub Page快

# 安装 Hugo

1. 前往 [Hugo Releases](https://github.com/gohugoio/hugo/releases)
2. 下载 hugo_extended_0.76.3_Windows-64bit.zip
3. 解压得到 hugo.exe
4. 将 hugo.exe 所在目录添加至系统环境变量
5. 在CMD中输入`hugo version`检查时候正确配置环境变量
6. 安装完成！


# 生成站点

使用Hugo快速生成站点，比如希望生成到 /path/to/site 路径：

`hugo new site /path/to/site`

这样就在 /path/to/site 目录里生成了初始站点，进去目录：

`cd /path/to/site`

站点目录结构：
```shell
.
├── archetypes   # 储存.md的模板文件，类似于hexo的scaffolds，该文件夹的优先级高于主题下的/archetypes文件夹
├── config.toml  # 配置文件
├── content      # 储存网站的所有内容，类似于hexo的source
├── data         # 储存数据文件供模板调用，类似于hexo的source/_data
├── layouts      # 储存.html模板，该文件夹的优先级高于主题下的/layouts文件夹
├── static       # 储存图片,css,js等静态文件，该目录下的文件会直接拷贝到/public，该文件夹的优先级高于主题下的/static文件夹
├── themes       # 储存主题
└── public       # 执行hugo命令后，储存生成的静态文件
```

# 安装主题
到[皮肤列表](https://themes.gohugo.io/)挑选一个心仪的皮肤，比如你觉得 hermit 皮肤不错，找到相关的 GitHub 地址，创建目录 themes，在 themes 目录里把皮肤 git clone 下来

```shell
# 创建 themes 目录
$ cd themes
$ git clone https://github.com/Track3/hermit
```

把 hermit 主题中的 exampleSite 文件夹中的内容复制(覆盖)到站点根目录下

> exampleSite文件夹在该路径下 themes/hermit/exampleSite
> 
> exampleSite文件夹中包含`config.toml`(主题站点配置文件)和`content`(示例文章)

对主题的修改主要通过`config.toml`配置文件，配置项说明参照注释

```toml
# baseURL用于渲染生成html文件中<link>,<img>等标签的链接
#baseURL = "https://kangvcar.github.io"
#baseURL = "https://kangvcar.vercel.app"
baseURL = "https://kangvcar.com"
# 配置站点语言，支持语言查看主题文件下的 i18n 文件夹
languageCode = "en-us"
defaultContentLanguage = "en"
# 配置浏览器标题
title = "Kang·Vcar"
# 配置主题
theme = "hermit"
# enableGitInfo = true
pygmentsCodefences  = true
pygmentsUseClasses  = true
hasCJKLanguage = true  # If Chinese/Japanese/Korean is your main content language, enable this to make wordCount works right.
rssLimit = 10  # Maximum number of items in the RSS feed.
copyright = "This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License." # This message is only used by the RSS template.
enableEmoji = true  # Shorthand emojis in content files - https://gohugo.io/functions/emojify/
# googleAnalytics = "UA-123-45"
# disqusShortname = "yourdiscussshortname"

# 用于文末显示作者
[author]
  name = "KangVcar"

[blackfriday]
  # hrefTargetBlank = true
  # noreferrerLinks = true
  # nofollowLinks = true

# 配置文章分类方法
[taxonomies]
  tag = "tags"
  # Categories are disabled by default.

# 配置主题参数
[params]
  dateform        = "Jan 2, 2006"
  dateformShort   = "Jan 2"
  dateformNum     = "2006-01-02"
  dateformNumTime = "2006-01-02 15:04"

  # Metadata mostly used in document's head
  # description = ""
  # images = [""]
  # 配置主题颜色
  themeColor = "#494f5c"

  # 配置主页的副标题
  homeSubtitle = "Focus on Linux, Python, DevOps, Machine Learning, Quantitative Trading."
  # 配置页脚的版权信息
  footerCopyright = ' &#183; <a href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank" rel="noopener">CC BY-NC 4.0</a>'
  # bgImg = ""  # Homepage background-image URL

  # Prefix of link to the git commit detail page. GitInfo must be enabled.
  # gitUrl = "https://github.com/username/repository/commit/"

  # Toggling this option needs to rebuild SCSS, requires Hugo extended version
  justifyContent = false  # Set "text-align: justify" to `.content`.

  relatedPosts = false  # Add a related content section to all single posts page

  code_copy_button = true # Turn on/off the code-copy-button for code-fields
  
  # Add custom css
  # customCSS = ["css/foo.css", "css/bar.css"]

  # Social Icons
  # Check https://github.com/Track3/hermit#social-icons for more info.
  [[params.social]]
    name = "twitter"
    url = "https://twitter.com/kangvcar"

  [[params.social]]
    name = "github"
    url = "https://github.com/kangvcar"

  [[params.social]]
    name = "email"
    url = "mailto:kangvcar@gmail.com"

  [[params.social]]
    name = "linkedin"
    url = "https://www.linkedin.com/in/hekangjian/"

  [[params.social]]
    name = "telegram"
    url = "https://t.me/anzelak"

# 配置菜单项，支持权重
[menu]
  [[menu.main]]
    name = "About Me"
    url = "about/"
    weight = 20

  [[menu.main]]
    name = "Posts"
    url = "posts/"
    weight = 40

  [[menu.main]]
    name = "Open Source Projects"
    url = "project/"
    weight = 10

  [[menu.main]]
    name = "Resume"
    url = "resume.pdf"
    weight = 30
```

# 创建页面

创建一个 about 页面：

`hugo new about.md`

about.md 自动生成到了 content/about.md ，打开 about.md 查看：

```markdown
+++
date = "2015-10-25T08:36:54-07:00"
draft = true
title = "about"

+++

正文内容
```

内容是 Markdown 格式的，+++ 之间的内容是 TOML 格式的，根据你的喜好，你可以换成 YAML 格式（使用 --- 标记）或者 JSON 格式。

# 创建文章

创建第一篇文章，放到 post 目录，方便之后生成聚合页面。

`hugo new posts/first.md`

打开编辑 post/first.md ：

```markdown
---
date: "2015-10-25T08:36:54-07:00"
title: "first"
---

### Hello Hugo

 1. aaa
 1. bbb
 1. ccc
```



# 运行Hugo

在你的站点根目录执行 Hugo 命令进行调试

`hugo server` 

浏览器里打开： http://localhost:1313 或 http://127.0.0.1:1313

> hugo server 会自动监听你的原始文稿，你在编辑原始.md文件时的变化都会实时的反映在网站上。如果你不希望启用这个功能你可以使用`hugo server --watch=false`命令。

# Hugo使用

这里节选出`new`与`server`这两个比较常用的命令和`-D`,`-E`,`-F`这三个参数的使用方法。更多的`hugo`命令可以参考[官方文档](https://gohugo.io/getting-started/usage/)。

```shell
使用方法:
  hugo
  hugo [flags]
  hugo [command]
  hugo [command] [flags]

节选的 command:
  new         为你的站点创建新的内容
  server      一个高性能的web服务器

节选的 flags:
  -D, --buildDrafts                包括被标记为draft的文章
  -E, --buildExpired               包括已过期的文章
  -F, --buildFuture                包括将在未来发布的文章

举几个栗子:
  hugo -D                          生成静态文件并包括draft为true的文章
  hugo new post/new-content.md     新建一篇文章
  hugo new site mysite             新建一个称为mysite的站点
  hugo server --buildExpired       启动服务器并包括已过期的文章
```

# 将站点的全部内容同步至GitHub

1. 确保已通过命令`hugo server`生成静态页面文件到 public 目录
2. 删除主题目录(themes/hermit)中`.git`文件
3. 在GitHub上创建一个Repository(eg: iblog)
4. 将站点的全部内容同步至GitHub，参照如下命令

```shell
cd 站点根目录
git init
git remote add origin git@github.com:kangvcar/iblog.git
git add -A
git commit -m "first commit"
git push -u origin master
```

# 部署到Vercel

1. 使用GitHub账号登录Vercel[^3]
2. 点击`Import Project` -> `Import Git Repository` -> 输入刚刚创建的仓库链接(eg: https://github.com/kangvcar/iblog)
3. 输入`PROJECT NAME`，选择`FRAMEWORK PRESET`为`Hugo`
4. 点击`Deploy`即可，部署成功后会自动分配一个vercel.com的二级域名，点击`Visit`即可访问站点
5. 可根据需要自定义域名（需同步修改站点配置文件`config.toml`中的`baseURL`参数为新域名
6. 至此部署成功，Vercel会自动监控GitHub仓库的变化并更新部署

# 参考链接

- https://olowolo.com/post/hugo-quick-start/
- https://juejin.im/post/6844903909333401614


[^1]: https://gohugo.io/
[^2]: https://github.com/
[^3]: https://vercel.com/
[^4]: https://gohugo.io/documentation/
[^5]: https://themes.gohugo.io/