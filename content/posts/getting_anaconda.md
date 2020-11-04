---
title: "Getting_Anaconda"
date: 2020-11-04T20:56:30+08:00
draft: true
toc: false
images:
tags: 
  - untagged
---

# Anaconda

Anaconda 可以便捷获取包且对包能够进行管理，同时对环境可以统一管理的发行版本。Anaconda包含了conda、Python在内的超过180个科学包及其依赖项。

# Installation

- macOS系统图形界面安装
    1. 前往[官方下载页面](https://link.zhihu.com/?target=https%3A//www.anaconda.com/downloads%23macos)下载。有两个版本可供选择：Python 3.6 和 Python 2.7。选择版之后点击“64-Bit Graphical Installer”进行下载。
    2. 完成下载之后，双击下载文件，在对话框中“Introduction”、“Read Me”、“License”部分可直接点击下一步
    3. “Destination Select”部分选择“Install for me only”并点击下一步。

        注意：若有错误提示信息“You cannot install Anaconda in this location”则重新选择“Install for me only”并点击下一步。

    4. “Installation Type”部分，可以点击“Change Install Location”来改变安装位置。标准的安装路径是在用户的家目录下。若选择默认安装路径，则直接点击“Install”进行安装。
    5. 等待“Installation”部分结束，在“Summary”部分若看到“The installation was completed successfully.”则安装成功，直接点击“Close”关闭对话框。
    6. 在mac的Launchpad中可以找到名为“Anaconda-Navigator”的图标，点击打开。
    7. 若“Anaconda-Navigator”成功启动，则说明真正成功地安装了Anaconda；如果未成功，请务必仔细检查以上安装步骤。

        提示：“Anaconda-Navigator”中已经包含“Jupyter Notebook”、“Jupyterlab”、“Qtconsole”和“Spyder”。

    8. 完成安装。
- macOS系统**命令行安装**
    1. 前往[官方下载页面](https://link.zhihu.com/?target=https%3A//www.anaconda.com/downloads%23macos)下载。有两个版本可供选择：Python 3.6 和 Python 2.7。选择版之后点击“64-Bit Command-Line Installer”进行下载。
    2. 完成下载之后，在mac的Launchpad中找到“其他”并打开“终端”。
        - 安装Python 3.6： `bash ~/Downloads/Anaconda3-5.0.1-MacOSX-x86_64.sh`
        - 安装Python 2.7： `bash ~/Downloads/Anaconda2-5.0.1-MacOSX-x86_64.sh`

        ⚠️注意：

        1. 首词bash也需要输入，无论是否用的Bash shell。
        2. 如果你的下载路径是自定义的，那么把该步骤路径中的 ~/Downloads 替换成你自己的下载路径。
    3. 安装过程中，看到提示“In order to continue the installation process, please review the license agreement.”（“请浏览许可证协议以便继续安装。”），点击“Enter”查看“许可证协议”。
    4. 在“许可证协议”界面将屏幕滚动至底，输入“yes”表示同意许可证协议内容。然后进行下一步。
    5. 安装过程中，提示“Press Enter to confirm the location, Press CTRL-C to cancel the installation or specify an alternate installation directory.”（“按回车键确认安装路径，按'CTRL-C'取消安装或者指定安装目录。”）如果接受默认安装路径，则会显示 **PREFIX=/home/<user>/anaconda<2 or 3>** 并且继续安装。安装过程大约需要几分钟的时间。

        建议：直接接受默认安装路径。

    6. 安装器若提示“Do you wish the installer to prepend the Anaconda install location to PATH in your /home/<user>/.bash_profile ?”（“你希望安装器添加Anaconda安装路径在 **/home/<user>/.bash_profile** 文件中吗？”），建议输入“yes”。

        ⚠️注意：

        1. 路径 **/home/<user>/.bash_profile** 中 **<user>** 即进入到家目录后你的目录名。
        2. 如果输入“no”，则需要手动添加路径。添加 **export PATH="/<path to anaconda>/bin:$PATH"** 在 **.bashrc** 或者 **.bash_profile** 中。其中， **<path to anaconda>** 替换为你真实的Anaconda安装路径。
    7. 当看到“Thank you for installing Anaconda!”则说明已经成功完成安装。
    8. 关闭终端，然后再打开终端以使安装后的Anaconda启动。
    9. 验证安装结果。可选用以下任意一种方法：
        - 在终端中输入命令 `condal list` ，如果Anaconda被成功安装，则会显示已经安装的包名和版本号。
        - 在终端中输入 `python` 。这条命令将会启动Python交互界面，如果Anaconda被成功安装并且可以运行，则将会在Python版本号的右边显示“Anaconda custom (64-bit)”。退出Python交互界面则输入 **exit()** 或 **quit()** 即可。
        - 在终端中输入 `anaconda-navigator` 。如果Anaconda被成功安装，则Anaconda Navigator的图形界面将会被启动。
- **Windows系统安装Anaconda**
    1. 前往[官方下载页面](https://link.zhihu.com/?target=https%3A//docs.anaconda.com/anaconda/install/windows)下载。有两个版本可供选择：Python 3.6 和 Python 2.7，选择版之后根据自己操作系统的情况点击“64-Bit Graphical Installer”或“32-Bit Graphical Installer”进行下载。
    2. 完成下载之后，双击下载文件，启动安装程序。

        ⚠️注意：

        1. 如果在安装过程中遇到任何问题，那么暂时地关闭杀毒软件，并在安装程序完成之后再打开。
        2. 如果在安装时选择了“为所有用户安装”，则卸载Anaconda然后重新安装，只为“我这个用户”安装。
    3. 选择“Next”。
    4. 阅读许可证协议条款，然后勾选“I Agree”并进行下一步。
    5. 除非是以管理员身份为所有用户安装，否则仅勾选“Just Me”并点击“Next”。
    6. 在“Choose Install Location”界面中选择安装Anaconda的目标路径，然后点击“Next”。

        ⚠️注意：

        1. 目标路径中**不能**含有**空格**，同时不能是**“unicode”**编码。
        2. 除非被要求以管理员权限安装，否则不要以管理员身份安装。
    7. 在“Advanced Installation Options”中**不要**勾选“Add Anaconda to my PATH environment variable.”（“添加Anaconda至我的环境变量。”）。因为如果勾选，则将会影响其他程序的使用。如果使用Anaconda，则通过打开Anaconda Navigator或者在开始菜单中的“Anaconda Prompt”（类似macOS中的“终端”）中进行使用。除非你打算使用多个版本的Anaconda或者多个版本的Python，否则便勾选“Register Anaconda as my default Python 3.6”。然后点击“Install”开始安装。如果想要查看安装细节，则可以点击“Show Details”。
    8. 点击“Next”。
    9. 进入“Thanks for installing Anaconda!”界面则意味着安装成功，点击“Finish”完成安装。

        ⚠️注意：如果你不想了解“Anaconda云”和“Anaconda支持”，则可以**不勾选**“Learn more about Anaconda Cloud”和“Learn more about Anaconda Support”。

    10. 验证安装结果。可选以下任意方法：
        1. “开始 → Anaconda3（64-bit）→ Anaconda Navigator”，若可以成功启动Anaconda Navigator则说明安装成功。
        2. “开始 → Anaconda3（64-bit）→ 右键点击Anaconda Prompt → 以管理员身份运行”，在Anaconda Prompt中输入 **conda list** ，可以查看已经安装的包名和版本号。若结果可以正常显示，则说明安装成功。

    ![https://pic4.zhimg.com/80/v2-c2599c8fd177949a7926ffbadb415887_1440w.jpg](https://pic4.zhimg.com/80/v2-c2599c8fd177949a7926ffbadb415887_1440w.jpg)

    ![https://pic4.zhimg.com/80/v2-9c5b812be9fce180687f4b61a0dc5e9f_1440w.jpg](https://pic4.zhimg.com/80/v2-9c5b812be9fce180687f4b61a0dc5e9f_1440w.jpg)

    ![https://pic4.zhimg.com/80/v2-cc3f1a3a0b9e91dfafcc8e6663aedbcf_1440w.jpg](https://pic4.zhimg.com/80/v2-cc3f1a3a0b9e91dfafcc8e6663aedbcf_1440w.jpg)

- **Linux系统安装Anaconda**
    1. 前往[官方下载页面](https://link.zhihu.com/?target=https%3A//www.anaconda.com/download/%23linux)下载。有两个版本可供选择：Python 3.6 和 Python 2.7。
    2. 启动终端，在终端中输入命令 `md5sum /path/filename 或 sha256sum /path/filename`

        ⚠️注意：将该步骤命令中的  /path/filename 替换为文件的实际下载路径和文件名。其中，path是路径，filename为文件名。

    3. 根据Python版本的不同有选择性地在终端输入命令：
        - Python 3.6： `bash ~/Downloads/Anaconda3-5.0.1-Linux-x86_64.sh`
        - Python 2.7： `bash ~/Downloads/Anaconda2-5.0.1-Linux-x86_64.sh`
    4. 安装过程中，看到提示“In order to continue the installation process, please review the license agreement.”（“请浏览许可证协议以便继续安装。”），点击“Enter”查看“许可证协议”。
    5. 在“许可证协议”界面将屏幕滚动至底，输入“yes”表示同意许可证协议内容。然后进行下一步。
    6. 安装过程中，提示“Press Enter to accept the default install location, CTRL-C to cancel the installation or specify an alternate installation directory.”（“按回车键确认安装路径，按'CTRL-C'取消安装或者指定安装目录。”）如果接受默认安装路径，则会显示 **PREFIX=/home/<user>/anaconda<2 or 3>** 并且继续安装。安装过程大约需要几分钟的时间。
    7. 安装器若提示“Do you wish the installer to prepend the Anaconda<2 or 3> install location to PATH in your /home/<user>/.bashrc ?”（“你希望安装器添加Anaconda安装路径在 **/home/<user>/.bashrc** 文件中吗？”），建议输入“yes”。

        ⚠️注意：

        1. 路径 **/home/<user>/.bash_rc** 中 **“<user>”** 即进入到家目录后你的目录名。
        2. 如果输入“no”，则需要手动添加路径，否则conda将无法正常运行。
    8. 当看到“Thank you for installing Anaconda<2 or 3>!”则说明已经成功完成安装。
    9. 关闭终端，然后再打开终端以使安装后的Anaconda启动。或者直接在终端中输入 **source ~/.bashrc** 也可完成启动。
    10. 验证安装结果。可选用以下任意一种方法：
        - 在终端中输入命令 `condal list` ，如果Anaconda被成功安装，则会显示已经安装的包名和版本号。
        - 在终端中输入 `python` 。这条命令将会启动Python交互界面，如果Anaconda被成功安装并且可以运行，则将会在Python版本号的右边显示“Anaconda custom (64-bit)”。退出Python交互界面则输入 **exit()** 或 **quit()** 即可。
        - 在终端中输入 `anaconda-navigator` 。如果Anaconda被成功安装，则Anaconda Navigator将会被启动。
- macOS Homebrew 安装Anaconda

    ```bash
    ## Mac安装大概步骤
    $ brew cask install anaconda
    ## 看情况添加环境变量
    $ echo 'export PATH=/usr/local/anaconda3/bin:$PATH' >> ~/.bash_profile
    $ source ~/.bash_profile
    ```

- 配置Anaconda使用清华镜像

    TUNA 提供了 Anaconda 仓库与第三方源（conda-forge、msys2、pytorch等）的镜像，各系统都可以通过修改用户目录下的 .condarc 文件。Windows 用户无法直接创建名为 .condarc 的文件，可先执行 conda config --set show_channel_urls yes 生成该文件之后再修改。

    修改 `.condarc` 文件内容如下，即可添加Anaconda Python免费仓库。

    ```bash
    channels:
      - defaults
    show_channel_urls: true
    channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
    default_channels:
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
    custom_channels:
      conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    ```

    运行 `conda clean -i` 清除索引缓存，保证用的是镜像站提供的索引。

    运行 `conda create -n myenv numpy` 测试一下吧。

# Usage

## **管理conda**

- **验证conda已被安装**

    `conda --version`

    终端上将会以 **conda 版本号** 的形式显示当前安装conda的版本号。如： **conda 3.11.0**

    ⚠️注意：如果出现错误信息，则需核实是否出现以下情况：

    1. 使用的用户是否是安装Anaconda时的账户。
    2. 是否在安装Anaconda之后重启了终端。
    - **更新conda至最新版本**

    `conda update conda`

    执行命令后，conda将会对版本进行比较并列出可以升级的版本。同时，也会告知用户其他相关包也会升级到相应版本。

    当较新的版本可以用于升级时，终端会显示 **Proceed ([y]/n)?** ，此时输入 **y** 即可进行升级。

- **查看conda帮助信息**

    `conda --help`

    或

    `conda -h`

- **卸载conda**
    1. **Linux 或 macOS**

        `rm -rf ~/anaconda2`

        或

        `rm -rf ~/anaconda3`

        即删除Anaconda的安装目录。根据安装的Anaconda版本选择相应的卸载命令。

    2. **Windows**

        `控制面板 → 添加或删除程序 → 选择“Python X.X (Anaconda)” → 点击“删除程序”`

        ⚠️注意：

        1. Python X.X：即Python的版本，如：Python 3.6。
        2. Windows 10的删除有所不同。

## 管理环境

- **创建新环境**

    `$ conda create --name <env_name> <package_names>`

    1.  可指定python版本号，如： `conda create -n my-env python=2.7`
    2. 如果要在新创建的环境中创建多个包，则直接在 <package_names> 后以空格隔开，添加多个包名即可。如： `conda create -n python3 python=3.5 numpy pandas`，即创建一个名为“python3”的环境，环境中安装版本为3.5的python，同时也安装了numpy和pandas。
    3. 默认情况下，新创建的环境将会被保存在 /Users/<user_name>/anaconda3/env 目录下，其中， <user_name> 为当前用户的用户名。
- **切换环境**
    1. **Linux 或 macOS**

        `$ conda activate <env_name>`

    2.  **Windows**

        `activate <env_name>`

    3. **提示**
        1. 如果创建环境后安装Python时没有指定Python的版本，那么将会安装与Anaconda版本相同的Python版本，即如果安装Anaconda第2版，则会自动安装Python 2.x；如果安装Anaconda第3版，则会自动安装Python 3.x。
        2. 当成功切换环境之后，在该行行首将以“(env_name)”或“[env_name]”开头。其中，“env_name”为切换到的环境名。如：在macOS系统中执行 `source active python2` ，即切换至名为“python2”的环境，则行首将会以(python2)开头。
- **退出环境至root**
    1.  **Linux 或 macOS**

        `conda deactivate`

    2. **Windows**

        `deactivate`

    3. **提示**

        当执行退出当前环境，回到root环境命令后，原本行首以“(env_name)”或“[env_name]”开头的字符将不再显示。

- **显示已创建环境**

    `conda info --envs`

    或

    `conda info -e`

    或

    `conda env list`

    结果中星号“*”所在行即为当前所在环境。macOS系统中默认创建的环境名为“base”。

- **复制环境**

    `conda create --name <new_env_name> --clone <copied_env_name>`

    ⚠️注意

    1.  **<copied_env_name>** 即为被复制/克隆环境名。
    2. **<new_env_name>** 即为复制之后新环境的名称。
    3.  **`conda create --name py2 --clone python2`**，即为克隆名为“python2”的环境，克隆后的新环境名为“py2”。此时，环境中将同时存在“python2”和“py2”环境，且两个环境的配置相同。
- **删除环境**

    `conda remove --name <env_name> --all`

    ⚠️注意：**<env_name>**为被删除环境的名称。

## 包管理

- **查找可供安装的包版本**
    1.  **精确查找**

        `conda search --full-name <package_full_name>`

        ⚠️注意：

        1.  **--full-name** 为精确查找的参数。
        2. **<package_full_name>** 是被查找包的**全名**。
        3. 例如：  `conda search --full-name python`即查找全名为“python”的包有哪些版本可供安装。
    2. **模糊查找**

        `conda search <text>`

        ⚠️注意：

        1.  **<text>** 是查找含有**此字段**的包名。
        2. 例如：  `conda search py`即查找含有“py”字段的包，有哪些版本可供安装。
- **获取当前环境中已安装的包信息**

    `conda list`

    执行上述命令后将在终端显示当前环境已安装包的包名及其版本号。

- **安装包**
    1. **在指定环境中安装包**

        `conda install --name <env_name> <package_name>`

        ⚠️注意：

        1. **<env_name>** 即将包安装的指定环境名。
        2. **<package_name>** 即要安装的包名。
        3. 例如：`conda install --name python2 pandas`即在名为“python2”的环境中安装pandas包。
    2. **在当前环境中安装包**

        `conda install <package_name>`

        ⚠️注意：

        1. **<package_name>** 即要安装的包名。
        2. 执行命令后在当前环境中安装包。
        3. 例如：  `conda install pandas`即在当前环境中安装pandas包。
    3. **使用pip安装包**

        **使用场景：**当使用 **conda install** 无法进行安装时，可以使用pip进行安装。例如：see包。

        **命令：**`pip install <package_name>`

        ⚠️注意：

        1.  **<package_name>** 为指定安装包的名称。
        2. 例如：  `pip install see`即安装see包。
        3. pip只是包管理器，无法对环境进行管理。因此如果想在指定环境中使用pip进行安装包，则需要先切换到指定环境中，再使用pip命令安装包。
            1. pip无法更新python，因为pip并不将python视为包。
        4. pip可以安装一些conda无法安装的包；conda也可以安装一些pip无法安装的包。因此当使用一种命令无法安装包时，可以尝试用另一种命令。
    4. **从[http://Anaconda.org](https://link.zhihu.com/?target=http%3A//Anaconda.org)安装包**

        **使用场景：**当使用 **conda install** 无法进行安装时，可以考虑从[http://Anaconda.org](https://link.zhihu.com/?target=http%3A//Anaconda.org)中获取安装包的命令，并进行安装。

        ⚠️**注意：**

        1. 从[http://Anaconda.org](https://link.zhihu.com/?target=http%3A//Anaconda.org)安装包时，无需注册。

        2. 在**当前环境**中安装来自于[http://Anaconda.org](https://link.zhihu.com/?target=http%3A//Anaconda.org)的包时，需要通过输入要安装的包在[http://Anaconda.org](https://link.zhihu.com/?target=http%3A//Anaconda.org)中的路径作为获取途径（channel）。查询路径的方式如下：

        1. 在浏览器中输入：[http://anaconda.org](https://link.zhihu.com/?target=http%3A//anaconda.org/)，或直接点击[Anaconda Cloud](https://link.zhihu.com/?target=http%3A//anaconda.org/)
        2. 在新页面“Anaconda Cloud”的上方搜索框中输入要安装的包名，然后点击右边“放大镜”标志。
        3. 搜索结果中有数以千计的包可供选择，此时点击“Downloads”可根据下载量进行排序，最上面的为下载最多的包。
        4. 选择满足需求的包或下载量最多的包，点击包名。
        5. 复制“To install this package with conda run:”下方的命令，并粘贴在终端中执行。
        6. 完成安装。
- **卸载包**
    1. **卸载指定环境中的包**

        `conda remove --name <env_name> <package_name>`

        ⚠️注意：

        1. **<env_name>** 即卸载包所在指定环境的名称。
        2. **<package_name>** 即要卸载包的名称。包名两边不加尖括号“<>”。
        3. 例如：  `conda remove --name python2 pandas`即卸载名为“python2”中的pandas包。
    2. **卸载当前环境中的包**

        `conda remove <package_name>`

        ⚠️注意：

        1. **<package_name>** 即要卸载包的名称。
        2. 执行命令后即在当前环境中卸载指定包。
        3. 例如： `conda remove pandas` 即在当前环境中卸载pandas包。
- **更新包**
    1. **更新所有包**

        `conda update --all`

        或

        `conda upgrade --all`

        ⚠️建议：在安装Anaconda之后执行上述命令更新Anaconda中的所有包至最新版本，便于使用。

    2. **更新指定包**

        `conda update <package_name>`

        或

        `conda upgrade <package_name>`

        ⚠️注意：

        1. **<package_name>** 为指定更新的包名。
        2. 更新多个指定包，则包名以**空格**隔开，向后排列。如： `conda update pandas numpy matplotlib`即更新pandas、numpy、matplotlib包。