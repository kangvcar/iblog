---
title: "制作Python-embed版InfoSpider程序"
date: 2020-10-20T10:10:17+08:00
draft: true
toc: false
images:
tags: 
  - untagged
---

## 1. 下载 embed 版 Python 

首先下载 embed 版 Python 并解压，[下载地址](https://www.python.org/downloads/windows/)

由于InfoSpider项目的开发环境时Python3.7，所以这里我就下载对应的embed版Python，如下图所示

<img src="https://i.loli.net/2020/10/19/xes4Bu31TS9zvVm.png" alt="image-20201019214340997" style="zoom:50%;" />

下载后得到`python-3.7.9-embed-amd64.zip`压缩文件，解压该压缩包得到完整的Python的环境，如下图所示

<img src="https://i.loli.net/2020/10/19/5OjlbrZ29sJMuXx.png" alt="image-20201019215107476" style="zoom:50%;" />

## 2. 下载 get-pip 并放入 embed 版 Python 文件夹中

`get-pip`是pip软件包管理工具的安装脚本，[下载地址](https://pip.pypa.io/en/latest/installing/)

```powershell
> cd .\embedInfoSpider
embedInfoSpider> curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

把下载`get-pip`文件放入embed 版 Python 文件夹中（我的文件夹是`embedInfoSpider`）

## 3. 修改 python**._pth文件

打开 embed 版 Python 中（我的文件夹是`embedInfoSpider`）的 python\*\*._pth，其中\*\*是版本号，掉 `import site` 前的注释。

<img src="https://i.loli.net/2020/10/19/78BQmgzAE4FpfGO.png" alt="image-20201019220659970" style="zoom:50%;" />

## 4. 安装 pip

在命令行运行命令`.\python.exe .\get-pip.py`进行安装pip

```powershell
> cd .\embedInfoSpider
embedInfoSpider> .\python.exe .\get-pip.py
```



## 5. 把InfoSpider项目放入embed 版 Python 文件夹中

从GitHub获取InfoSpider项目源码到embed 版 Python 文件夹中（我的文件夹是`embedInfoSpider`），命令如下

```powershell
> cd .\embedInfoSpider
embedInfoSpider> git clone https://github.com/kangvcar/InfoSpider.git
```



## 6. 安装需要的 Python 模块

在命令行运行`.\python.exe -m pip install 模块名 -i https://pypi.doubanio.com/simple --no-warn-script-location`进行安装需要的Python模块

其中InfoSpider项目的需要的Python模块信息在InfoSpider项目中的`requirements.txt`，使用如下命令安装InfoSpider项目的需要的Python模块

```powershell
> cd .\embedInfoSpider
embedInfoSpider> .\python.exe -m pip install -r .\InfoSpider\requirements.txt -i https://pypi.doubanio.com/simple --no-warn-script-location
```

***这一步的常见错误参见文末***

## 7. 安装Tkinter环境

假设您在 Windows 上还安装了常规 Python 版本（与嵌入式Python具有相同版本），在嵌入式Python中安装 Tkinter，可以从常规 Python 版本中复制以下文件：

- 复制常规 Python 版本根目录下的`tcl`目录到嵌入式Python版本的根目录下
- 复制常规 Python 版本中`Lib\tkinter`目录到嵌入式Python版本的根目录下
- 复制常规 Python 版本中`DLLs`目录下的三个文件`_tkinter.pyd` `tcl86t.dll` `tk86t.dll` 到嵌入式Python版本的`DLLs`目录下
- 在嵌入式Python版本根目录下的`python**._pth`中添加`.\DLLs`

参考链接

- https://stackoverflow.com/questions/37710205/python-embeddable-zip-install-tkinter
- https://zhuanlan.zhihu.com/p/77338198

## 8. 安装ChromeDriver

安装[Chrome](https://www.google.com/chrome/)浏览器并下载对应Chrome浏览器版本的[ChromeDriver](https://chromedriver.chromium.org/downloads)，然后将下载的 chromedriver.exe 放入嵌入式Python的根目录下

## 9. 编写程序启动脚本

编写`start.bat`启动脚本文件，内容如下

```vbscript
@.\python.exe .\InfoSpider\tools\main.py
@pause
```

## 10. 启动InfoSpider程序

进入嵌入式Python版本的根目录下，双击`start.bat`文件即可运行程序

-----

### 报错一：

```powershell
  Downloading https://pypi.doubanio.com/packages/22/28/a99c42aea746e18382ad9fb36f64c1c1f04216f41797f2f0fa567da11388/et_xmlfile-1.0.1.tar.gz (8.4 kB)
    ERROR: Command errored out with exit status 1:
     command: 'C:\Users\kangv\Desktop\embedInfoSpider\python.exe' -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'C:\\Users\\kangv\\AppData\\Local\\Temp\\pip-install-my2a4wz_\\et-xmlfile\\setup.py'"'"'; __file__='"'"'C:\\Users\\kangv\\AppData\\Local\\Temp\\pip-install-my2a4wz_\\et-xmlfile\\setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base 'C:\Users\kangv\AppData\Local\Temp\pip-pip-egg-info-6q3fazxu'
         cwd: C:\Users\kangv\AppData\Local\Temp\pip-install-my2a4wz_\et-xmlfile\
    Complete output (5 lines):
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "C:\Users\kangv\AppData\Local\Temp\pip-install-my2a4wz_\et-xmlfile\setup.py", line 40, in <module>
        from et_xmlfile import (
    ModuleNotFoundError: No module named 'et_xmlfile'
    ----------------------------------------
ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.
```

先[下载](https://files.pythonhosted.org/packages/22/28/a99c42aea746e18382ad9fb36f64c1c1f04216f41797f2f0fa567da11388/et_xmlfile-1.0.1.tar.gz)`et_xmlfile`模块的tar.gz包到python根目录并解压之得到 et_xmlfile-1.0.1 文件夹，在 et_xmlfile-1.0.1 文件夹中找到 setup.py 文件，然后修改 setup.py 文件，在导入 et_xmlfile 模块前添加如下一行代码

```python
# new line:
sys.path.insert(0, here)

from et_xmlfile import (
```

然后在命令行中进行安装`et_xmlfile`模块，命令如下

````powershell
> cd .\embedInfoSpider
embedInfoSpider> .\python.exe .\et_xmlfile-1.0.1\setup.py install
````

参考链接

- https://superuser.com/questions/1432693/pip-install-et-xmlfile-failure

### 报错二：

```powershell
Collecting future
    ERROR: Command errored out with exit status 1:
\Temp\\pip-install-vghmcrxf\\future\\setup.py'"'"'; __file__='"'"'C:\\Users\\kangv\\AppData\\Local\\Temp\\pip-install-vghmcrxf\\future\\setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base 'C:\Users\kangv\AppData\Local\Temp\pip-pip-egg-info-0aop6rx3'
         cwd: C:\Users\kangv\AppData\Local\Temp\pip-install-vghmcrxf\future\
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "C:\Users\kangv\AppData\Local\Temp\pip-install-vghmcrxf\future\setup.py", line 86, in <module>
        import src.future
    ----------------------------------------
ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.
```

先[下载](https://files.pythonhosted.org/packages/45/0b/38b06fd9b92dc2b68d58b75f900e97884c45bedd2ff83203d933cf5851c9/future-0.18.2.tar.gz)`future`模块的tar.gz包到python根目录并解压之得到 future-0.18.2 文件夹，在 future-0.18.2 文件夹中找到 `setup.py` 文件，然后修改 `setup.py` 文件，在导入 `future` 模块前添加如下一行代码

```python
# new line:
sys.path.append('.')

import src.future
```

然后在命令进行安装 `future` 模块，注意要在 future-0.18.2 目录下执行安装命令

```powershell
> cd .\embedInfoSpider
embedInfoSpider> cd .\future-0.18.2
embedInfoSpider\future-0.18.2> ..\python.exe .\setup.py install
```

参考链接

- https://stackoverflow.com/questions/57078689/module-not-found-error-no-module-named-src
- https://stackoverflow.com/questions/51049663/python3-6-error-modulenotfounderror-no-module-named-src