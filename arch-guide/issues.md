这里是我遇到的问题以及对应的解决方案

- [efibootmgr里面有超级多启动项](#efibootmgr里面有超级多启动项)
- [KDE开机会卡住，必须重启sddm才好](#kde开机会卡住必须重启sddm才好)
- [磁盘占用异常](#磁盘占用异常)
- [提示没有编解码器](#提示没有编解码器)
- [NAUTILUS无法访问smb共享](#nautilus无法访问smb共享)
- [域名解析出现暂时性错误](#域名解析出现暂时性错误)
- [扩展windwos的efi分区空间](#扩展windwos的efi分区空间)
- [grub卡顿](#grub卡顿)
- [virsh不显示虚拟机加上sudo后显示](#virsh不显示虚拟机加上sudo后显示)
- [GNOME混合模式独显占用异常](#gnome混合模式独显占用异常)
- [fuzzel无法打开终端程序](#fuzzel无法打开终端程序)
- [thuanr 的压缩解压缩软件不生效](#thuanr-的压缩解压缩软件不生效)
- [nautilus等gtk4应用启动慢](#nautilus等gtk4应用启动慢)
- [gnome的设置中心无法在窗口管理器打开](#gnome的设置中心无法在窗口管理器打开)
- [软件缩放问题](#软件缩放问题)
- [btrfs-assistant没有同步matugen主题](#btrfs-assistant没有同步matugen主题)
- [gtk4软件无法即时切换主题](#gtk4软件无法即时切换主题)
- [qq用wayland运行时剪贴板异常](#qq用wayland运行时剪贴板异常)
- [quickshell图标缺失](#quickshell图标缺失)
- [networkmanager切换到iwd后端后使用impala联网提示operation aborted](#networkmanager切换到iwd后端后使用impala联网提示operation-aborted)
- [obs导致显卡占用飙升进而导致游戏帧数大幅下降](#obs导致显卡占用飙升进而导致游戏帧数大幅下降)
- [天选4锐龙版2023使用Niri关闭屏幕后会自己亮屏](#天选4锐龙版2023使用niri关闭屏幕后会自己亮屏)

## efibootmgr里面有超级多启动项

这是之前其他系统和网络设备的残留的nvram。用efibootmgr清理。

用这条命令列出所有的启动项

```
sudo efibootmgr -v
```

找到无用的项目对应的编号删除

```
sudo efibootmgr -b 0001 -B
```

此处的0001是编号

## KDE开机会卡住，必须重启sddm才好

显卡驱动没加载完sddm就加载导致的卡死。让sddm晚点加载就可以解决。

```
sudo systemctl edit sddm.service
```

```
[Service]
ExecStartPre=/bin/sleep 2
```

```
sudo systemctl daemon-reload
```

或者试试参考这个issue：[24#issue](https://github.com/SHORiN-KiWATA/ShorinArchExperience-ArchlinuxGuide/issues/24#issue-3745629323)

## 磁盘占用异常

明明没有多少文件，磁盘占用却很高。可以试试删除btrfs快照。

## 提示没有编解码器

安装的时候应该自带了编解码器，可能是删除别的软件时不小心连带着删掉了，重新安装皆可

```
sudo pacman -S gst-plugins-good gst-libav libde265
```

## NAUTILUS无法访问smb共享

如果你的路由器或者别的设备开启了smb文件共享，安装gvfs-smb可以使你在nautilus访问那些文件

```
sudo pacman -S gvfs-smb
```

## 域名解析出现暂时性错误

[解决 Ubuntu 系统中 “Temporary Failure in Name Resolution“ 错误-CSDN博客](https://blog.csdn.net/qq_15603633/article/details/141032652)

```
sudo vim /etc/resolv.conf
```

内容修改为

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

## 扩展windwos的efi分区空间

NIUBI partition Editor free edition 使用这个工具

## grub卡顿

n卡的锅，没辙

## virsh不显示虚拟机加上sudo后显示

因为虚拟机被创建在系统范围的qemu连接里了

```
sudo vim /etc/environment
```

写入

```
VIRSH_DEFAULT_CONNECT_URI=qemu:///system
```

## GNOME混合模式独显占用异常

```
sudo pacman -S vulkan-mesa-layers
```

## fuzzel无法打开终端程序

```
vim .config/fuzzel/fuzzel.ini
```

```
terminal=kitty -e
```

## thuanr 的压缩解压缩软件不生效

配置自定义动作

- 解压到此处

   ```
   file-roller --extract-here %F
   ```

   出现条件勾选除了目录之外的，然后文件类型填写：

   ```
   *.zip;*.tar;*.tar.gz;*.tgz;*.tar.bz;*.tbz;*.tar.bz2;*.tbz2;*.tar.Z;*.taz;*.tar.xz;*.tar.lz;*.tlz;*.tar.lzo;*.tzo;*.tar.lzma;*.7z;*.rar;*.cbr;*.cab;*.arj;*.cpio;*.deb;*.rpm;*.xar;*.jar;*.war;*.ear;*.iso;*.lha;*.lzh;*.alz;*.ace;*.zoo;*.cbz;*.gz;*.bz;*.bz2;*.xz;*.Z;*.lz;*.lzo;*.lzma;*.zst;*.br;*.lrz;*.rzip
   ```

- 创建压缩包

   ```
   file-roller --add %F
   ```

   出现条件勾选所有

## nautilus等gtk4应用启动慢

因为gtk4使用了新的渲染器，而新的渲染器和n卡的'nvidia-utils'产生了兼容性问题，设置环境变量使用旧的gl渲染器可以解决。

```
env GSK_RENDERER=gl nautilus
```

也可以在`/etc/environment`设置全局环境变量或者在`~/.config/environment.d/myenv.conf`设置用户空间的环境变量（需要重新登录）。

窗口管理器可以在配置文件里修改环境变量。

- niri

   ```
   environment {
      GSK_RENDERER "gl"
   }
   ```

## gnome的设置中心无法在窗口管理器打开

因为gnome-control-center只能在gnome桌面环境打开

```
env XDG_CURRENT_DESKTOP=gnome gnome-control-center
```

## 软件缩放问题

详情看这个网址：<https://wiki.archlinuxcn.org/wiki/HiDPI#GTK+_2>

[archwiki_hdpi](https://wiki.archlinux.org/title/HiDPI)

最常见的fcitx5候选框过小问题也可以使用这个方法

1. 安装xorg-xrdb

   ```
   sudo pacman -S xorg-xrdb
   ```

2. 计算dpi

   用标准dpi乘屏幕缩放，标准dpi通常是96。1.5倍缩放就是：96x1.5=144

3. xrdb调整缩放

   - 命令

      ```
      echo "Xft.dpi: 144" | xrdb -merge
      ```

      运行这个只是临时生效，想永久生效的话可以设置此命令自动启动。

   - 配置文件

      `~/.Xresources`里写入：Xft.dpi: 144。

## btrfs-assistant没有同步matugen主题

因为是以root运行的，root用户有自己的配置文件夹。

```
sudo ln -sf $HOME/.config/qt5ct /root/.config/qt5ct
sudo ln -sf $HOME/.config/qt6ct /root/.config/qt6ct
sudo ln -sf $HOME/.config/kdeglobals /root/.config/kdeglobals
```

## gtk4软件无法即时切换主题

可以通过切换暗色浅色的方式做到即时切换

```
gsettings set org.gnome.desktop.interface color-scheme "prefer-light" && gsettings set org.gnome.desktop.interface color-scheme "prefer-dark"
```

## qq用wayland运行时剪贴板异常

因为此时qq无法使用wayland剪贴板，且因为是wayland运行，无法触发wayland和xwayland的剪贴板同步功能。

尝试以下操作：

1. qq里复制点东西

2. 终端打印x11剪贴板里的内容

   - 文字

      ```
      xclip -o -sel clip

      # xclip管理x11剪贴板的cli
      # -o（output） 输出剪贴板数据
      # -sel（selection）选择要使用的剪贴板，不指定的话默认是鼠标中键的缓冲区。
      # clip（clipboard）代表ctrl+C/V的那个剪贴板
      ```

      此时会输出复制的文字

   - 图片

      ```
      xclip -selection clipboard -t image/png -o
      
      # 输出系统剪贴板数据
      # -t 指定数据类
      # xclip没有wl-paste智能，不指定类型为图片的话输出图片数据会报错
      ```

      此时会输出复制的图片的二进制数据

   - 查看类型

      ```
      xclip -selection clipboard -t TARGETS -o
      ```

3. 把刚刚打印的东西传给wayland剪贴板

   wl-copy会自动检测类型，所以通常不需要手动指定数据类型。

   ```
   xclip -o -sel clip | wl-copy
   ```

   ```
   xclip -selection clipboard -t image/png -o | wl-copy 
   ```

   此时你会发现qq复制的内容出现在了wayland的剪贴板里。

以上就是原理。只要在检测到x11剪贴板发生变化的时候自动完成以上操作就解决了qq的剪贴板问题。

## quickshell图标缺失

添加环境变量

- Niri

   ```
   environment{
      QS_ICON_THEME "Adwaita"
   }
   ```

## networkmanager切换到iwd后端后使用impala联网提示operation aborted

删除`/etc/NetworkManager/system-connections`下记忆的wifi，重启服务之后即可联网。

```
sudo rm -rf /etc/NetworkManager/system-connections/*
sudo systemctl restart NetworkManager
```

## obs导致显卡占用飙升进而导致游戏帧数大幅下降

游戏帧数大幅下降的根本原因是显卡占用过高导致画面输出堵塞。

有以下几个原因（以780M核显为例）：

- 第三方插件

   例如NDI（`distroav`），这类插件即使不激活，仅仅只是安装上，在开启obs之后就会导致显卡占用增加。

- 屏幕采集（13%）

   obs的屏幕采集极其吃显卡性能，你可以测试一下，开关屏幕采集，观察显卡占用的变化。窗口采集（5%）的占用要远远少于显示器采集。

- osb的预览画面（5%）

   obs的预览画面也相当吃显卡资源。

- obs本身

   如果obs的窗口开在前台，就会占用大量显卡资源，必须放在后台。

所以obs的正确使用方法是，用窗口采集获取画面，确认排版没有问题后关闭预览，然后然后把obs的窗口关到后台。

## 天选4锐龙版2023使用Niri关闭屏幕后会自己亮屏

是没能正确识别主板的传感器轮询导致该轮询被视为了一次未知的按键输入，代码`240/0xf0`。

如果你的情况和我相同，可以尝试屏蔽此按键：

```
sudo vim /etc/libinput/local-overrides.quirks
```
```
[Asus WMI Hotkeys Ignore Phantom]
MatchName=*Asus WMI hotkeys*
AttrEventCode=-EV_KEY:0xf0;
```
重启后可以解决问题。
