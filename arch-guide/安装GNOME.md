目录

- [安装基础组件](安装GNOME#1-安装)
- [配置GDM服务](安装GNOME#2-临时开启gdm)
- [网络工具(nm-connection-editor)](安装GNOME#安装高级网络配置工具nm-connection-editor)
- [分数缩放与VRR](安装GNOME#可变刷新率和分数缩放)
- [修改默认终端](安装GNOME#修改gnome默认终端)
- [生成Home目录](安装GNOME#生成home下目录如果没有的话)

---

[Archwiki_GNOME](https://wiki.archlinux.org/title/GNOME)

这个部分是安装GNOME，以及一些最基本的配置。

1. 安装

   ```
   pacman -S gnome-shell gdm ghostty gnome-control-center bazaar flatpak file-roller nautilus-python firefox 
   ```

   >`gnome-shell`最小化安装gnome；

   >`gdm` 是显示管理器(gnome display manager)；

   >`ghostty` 是一个可高度自定义，主打无缝融入任何环境的终端模拟器（terminal emulator）。当然，如果你需要可以自行更换；

   >`gnome-control-center` 是设置中心；

   >`bazaar` 是软件商城；

   >`nautilus-python`开启ghostty在文档管理器右键从此处打开终端的功能，对于其他终端，还需要`nautilus-open-any-terminal`；

   >`flatpak` 是flatpak软件，这是一种全发行版通用的软件打包形式，软件沙盒运行；

   >`file-roller`是和gnome的文档管理器集成的压缩解压缩工具；

   >`firefox`是linux上最好用的浏览器。

2. 临时开启gdm

   ```
   systemctl start gdm 
   ```

3. 登录普通用户账号

4. 设置gdm开机自启

   正常开启桌面后打开ghostty设置gdm

   ```
   sudo systemctl enable gdm
   ```

### 安装高级网络配置工具nm-connection-editor

```
sudo pacman -S --needed nm-connection-editor dnsmasq
```

### 可变刷新率和分数缩放

商店安装refine修改

```
flatpak install flathub page.tesk.Refine
```

### 修改gnome默认终端

- 方法一：dconf-editor

  ```
  sudo pacman -S dconf-editor
  ```

  org.gnome.desktop.applications.terminal里的exec取消“使用默认值”，自定义值填ghostty，exec-arg同理，自定义值改成-e。这么写是因为别的程序调用ghostty运行命令时需要通过-e参数把命令传给ghostty。

- 方法二：gsettings命令

  ```
  gsettings set org.gnome.desktop.default-applications.terminal exec 'ghostty'
  gsettings set org.gnome.desktop.default-applications.terminal exec-arg '-e'
  ```

两个方法效果是一样的，可以运行这段命令查看是否修改成功：

```
gsettings get org.gnome.desktop.default-applications.terminal exec
gsettings get org.gnome.desktop.default-applications.terminal exec-arg
```

输出应该为

```
‘ghostty’
’-e‘
```

### 生成home下目录（如果没有的话）

```
sudo pacman -S --needed xdg-user-dirs

LANG=en_US.UTF-8 xdg-user-dirs-update --force
```

### 修改系统语言为中文

打开Settings，System --> Region & Language

### 接下来你自己根据需求决定安装什么软件，进行什么配置。当然，你也可以选择参考我的

## 下一节：[软件安装相关](软件安装相关)
