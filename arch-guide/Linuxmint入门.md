LinuxMint是最适合新手的发行版。本文是安装LinuxMint之后必要的基本配置。

[点击跳转视频教程](https://www.bilibili.com/video/BV19DBqB4EY4/?vd_source=65a8f230813d56660e48ae1afdfa4182#reply115786966372305)

[更详细的日常使用参考视频](https://www.bilibili.com/video/BV1YvenzUEFf/?spm_id_from=333.1387.upload.video_card.click)

## 镜像源和系统更新

首先在欢迎程序点击`第一步`-->`更新管理器`-->`确定`。

会提示切换到本地镜像，点击`主要`和`基础`边上的链接，稍微等待一会，测速完成后选择最快的镜像源。

选择完成后点击右下角`更新APT缓存`-->`应用更新`-->`安装更新`

## 安装驱动

在欢迎程序打开驱动管理器安装驱动。第一次打开需要加载一会，耐心等待吧。

## 中文输入法

1. 打开系统设置，点击`输入法`-->`简体中文`-->`安装`

2. 切换顶部的`输入法框架`为`fcitx`

3. 注销重新登录

默认的切换快捷键是`ctrl+空格`

## 软件安装

有几种方法

1. 软件管理器安装

2. 软件管理器安装flatpak包

    flatpak是众多发行版通用的打包方式。打开软件管理器，点击右上角三条横杠-->`首选项`-->`显示未经验证的flatpak软件`，这样可以显示出更多常用软件。

3. 网上下载deb包双击安装

    deb包就有点类似windows里的.exe或.msi安装包。

4. 网上下载appimage包

    下载之后需要右键设置`以可执行程序启动`。appimage可以类比为绿色免安装版。

5. 命令行安装

    - 搜索

        `sudo apt search 关键词`

    - 安装

        `sudo apt install 包名`

    - 卸载

        `sudo apt remove 包名`

`sudo`命令以管理员权限运行命令，需要输入密码，输入密码的过程不显示。

`apt`是debian系发行版的包管理器。

后面的`search` `install` `remove`都是`apt`的选项，更多选项可以通过`apt -h`查看，看不懂英文可以ai翻译。
