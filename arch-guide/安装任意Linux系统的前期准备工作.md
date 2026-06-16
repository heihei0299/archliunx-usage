## 下载ISO

去想要安装的Linux系统的官网下载ISO文件。官网下载慢的话可以找一找镜像站，主流Linux发行版通常会在下载页面提供镜像站链接。

- [Arch Linux](https://archlinux.org/download/)
- [Linux Mint](https://linuxmint.com/download.php)
- [CachyOS](https://cachyos.org/download/)

## 制作系统盘

**⚠️警告：注意备份重要数据⚠️**

- 方法一：ventoy

    [ventoy/Ventoy: A new bootable USB solution.](https://www.ventoy.net/cn/index.html)

    ventoy制作的系统盘可以存放多个系统镜像，推荐。

    不知道怎么下载的话下载[图吧工具箱](https://www.tbtool.cn/)，在“其他工具”页面里有ventoy。

- 方法二 ：压缩卷（没有u盘使用这个方法）

    1. windows系统内win+x键，选择磁盘管理。找到想安装archlinux的位置，右键选择压缩卷，空出磁盘空间。

    2. 右击空出的空间选择新增简单卷，大小设置为4096MB（足够装下iso里面的文件就行），盘符随意，格式化选择FAT32。

    3. 双击打开iso，把里面的内容粘贴进刚刚新建的盘符里。

## windows下的准备工作

如果你要安装双系统，以下工作是必须的。

1. 解决安装Linux后Windows时间错乱

    >[双系统时间同步-CSDN博客](https://blog.csdn.net/zhouchen1998/article/details/108893660) | [系统时间 - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/%E7%B3%BB%E7%BB%9F%E6%97%B6%E9%97%B4)

    Linux把主板时间改成标准UTC时间，然后根据系统设置的时区对UTC时间进行加减后显示出来。Windows直接读取主板时间显示出来，所以此时你Windows显示的时间就变成了UTC时间，表面上看就像是windows时间错乱了。Windows下管理员身份打开powershell 运行：

    ```powershell
    Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```

    <details close><summary>命令解释</summary>

    >`Reg add` 添加一个注册表项目

    >`HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation`是路径

    >`/v RealTimeIsUniversal` /v指定项目名称

    >`/t REG_DWORD` /t指定数据类型为REG_DWORD（32位无符号整数）

    >`/d 1` /d指定具体的值，1代表启用

    上面这条命令修改注册表，让Windows采用和Linux相同的策略，默认主板时间为UTC，根据系统设置的时区进行相应的加减后再显示。
    </details>

    另一种方法是让Linux使用本地时间`timedatectl set-local-rtc 1`，我没试过，说不定会在日志、定时任务之类的地方出问题。由于我主要使用Linux，所以使用上面的方法。

2. 关闭快速启动

    搜索`电源` --> `选择电源计划` --> `选择电源按钮的功能` --> `更改当前不可用的设置` --> 确认关闭了`快速启动`

    快速启动会锁定硬盘分区，导致Linux无法挂载Windows分区，甚至无法识别Wifi网卡。

3. 关闭Bitlocker

    系统设置里搜索到后关闭。Bitlocker加密会阻碍Linux访问硬盘，调整分区大小时可能导致数据丢失。

4. 预留硬盘空间

    右键windowslogo打开磁盘管理。右键你要使用的硬盘分区，通过压缩卷腾出给Linux空间。

## BiOS设置

重启电脑进入BIOS。不同的机器进bios方法不同，现代电脑通常是esc、f2、f7、delete，如果不行的查找一下自己主板进bios的方法

1. 关闭安全启动（security boot）

    > 部分发行版已支持安全启动，可以查阅官方文档。

    重启电脑进入BIOS关闭安全启动。

2. 关闭TPM

    开启TPM可能会在Linux下出现异常。

3. 调整启动项顺序

    启动项第一个设置成刚刚制作的系统盘。

4. 保存并退出

    通常是F10键

## 下一节：[Arch Linux安装教程](安装ArchLinux) | [Linux Mint入门指南](./Linuxmint入门) | [CachyOS 入门指南](./CachyOS)

