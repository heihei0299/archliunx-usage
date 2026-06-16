目录
- [⚠️这一章内容已经严重过时](#️这一章内容已经严重过时)
  - [可选：更换shell为fish](#可选更换shell为fish)
  - [安装hyprland](#安装hyprland)
  - [修改系统语言](#修改系统语言)
  - [显示器设置](#显示器设置)
  - [禁用鼠标加速](#禁用鼠标加速)
  - [触摸板自然滚动](#触摸板自然滚动)
  - [xwayland缩放问题](#xwayland缩放问题)
  - [安装必要软件](#安装必要软件)
  - [GUI文档管理器](#gui文档管理器)
    - [右键从此处打开终端](#右键从此处打开终端)
    - [更改默认终端为kitty](#更改默认终端为kitty)
    - [生成home目录下的目录](#生成home目录下的目录)
  - [桌面套件](#桌面套件)
    - [程序启动器](#程序启动器)
  - [蓝牙](#蓝牙)
  - [锁屏](#锁屏)
  - [剪贴板](#剪贴板)
  - [截图](#截图)
  - [中文输入法](#中文输入法)
  - [flatpak](#flatpak)
  - [下一节：软件安装相关](#下一节软件安装相关)

# ⚠️这一章内容已经严重过时

hyprland从0.55版本开始用lua替代原本的hyprlang作为配置文件的语言，配置流程不变，但配置的具体写法已经完全不同，我还没更新。

---
这部分是在tty从零开始安装一个**功能完备**的hyprland，以方便为主而不是以符合wm的理念为主，如果已经安装了其他桌面环境的话有些流程会略有不同。


## 可选：更换shell为fish

[FishShell](https://fishshell.com/)

由于要频繁用到终端，安装fish更加便利。

安装
```
sudo pacman -S fish
```
运行fish命令就可以打开fish了。

如果想在打开终端的时候自动进入fish的话可以设置类似`kitty -e fish` `ghostty -e fish`这样的快捷键。

## 安装hyprland

[Hyprland-Installation](https://wiki.hypr.land/Getting-Started/Installation/)

```
sudo pacman -S hyprland kitty firefox
```
>`hyprland`本体

>`kitty`是hyprland默认的终端仿真器，如果你已经安装了别的桌面环境可以不装`kitty`。

>`firefox`是linux最好用的浏览器

- 基础使用方法

   tty运行`start-hyprland`命令打开会话，或者在显示管理器切换为hyprland会话。super+Q打开kitty；super+C关闭窗口。

   虽然hyprland新版本增加了引导程序，但是为了学会使用WM，这里依旧手动配置。

- 修改默认终端

   1. 运行一次`start-hyprland`生成默认配置文件

   2. super+M退出hyprland

   3. 编辑配置文件

      ```
      vim ~/.config/hypr/hyprland.conf
      ```
      把`$terminal= kitty`的`kitty`改成你使用的终端。
   
- 设置kitty默认shell为fish

   ```
   mkdir -p ~/.config/kitty/kitty.conf
   vim ~/.config/kitty/kitty.conf
   ```

   ```
   shell fish
   ```
   
   美化kitty的部分在这里：[kitty美化](终端美化#Kitty美化)

## 修改系统语言

利用配置文件提供的环境变量功能设置LANG系统语言。设置时一定要让LC_CTYPE为英文，否则中文输入法会出现异常。

配置文件顶端写入

```
env = LANG,zh_CN.UTF-8
env = LC_CTYPE,en_US.UTF-8
```
重新开启hyprland即可。

- 如果是archinstall安装，需要进行如下操作：

  1. ```
     sudo vim /etc/locale.gen 
     ```

  2. 左斜杠键搜索，取消`zh_CN.UTF-8`的注释

  3. ```
     sudo locale-gen
     ```

  4. 登出

## 显示器设置

[Hyprland-Monotors](https://wiki.hypr.land/Configuring/Monitors/)

```
hyprctl monitors all
```
假设是eDP-1，分辨率2k，刷新率165
```
vim ~/.config/hypr/hyprland.conf
```
```
monitor=eDP-1,2560x1440@165,0x0,1.6
```
从左到右分别是 "显示器，分辨率@刷新率，位置，缩放“。

如果是多显示器的话，把最左上角的显示器的位置设置为0x0。假设eDP-1在最左上角，我第二个显示器想放在eDP-1的右边，就计算一下x的值，2560/1.6=1600，第二个显示器位置的x值就是1600。y同理。

```
monitor=DP-2,2560x1440@180,1600x0,1
```

## 禁用鼠标加速

[Hyprland-Input](https://wiki.hypr.land/Configuring/Variables/#input)

```
vim ~/.config/hypr/hyprland.conf
```

修改`input{}`，写入`accel_profile = flat`

```
input{
    accel_profile = flat
}
```
## 触摸板自然滚动

`natural_scroll=false`的`false`改成`true`

## xwayland缩放问题

[Hyprland-XWayland](https://wiki.hypr.land/Configuring/XWayland/)

xwayland软件在hyprland会像素化，需要在hyprland配置文件新增`xwayland{}`，填入`force_zero_scaling=true`

```
xwayland{
	force_zero_scaling=true
}
```
然后可以使用软件自己的缩放功能

或者使用`xrdb`

```
sudo pacman -S xorg-xrdb
```
```
echo "Xft.dpi: 140" | xrdb -merge
```
但是xrdb的效果并不好

## 安装必要软件

[Hyprland-Must-have](https://wiki.hypr.land/Useful-Utilities/Must-have/)

[Archwiki-XDG-Desktop-Portal](https://wiki.archlinux.org/title/XDG_Desktop_Portal)

1. 安装

    ```
    sudo pacman -S libnotify xdg-desktop-portal-hyprland hyprpolkitagent qt5-wayland qt6-wayland
    ```
    
    `libnotify`是通知相关的库

    `xdg-desktop-portal-hyprland` `xdg-desktop-portal-gtk`提供屏幕分享、全局快捷键、选取文件等功能。如果你已经安装了桌面环境这里装只装hyprland的portal就可以了。

    `hyprpolkitagent` 软件会通过这个软件询问管理员权限

    `qt5-wayland qt6-wayland` qt wayland库

2. 配置重要程序开机自启

    ```
    vim ~/.config/hypr/hyprland.conf
    ```
    
    搜索`exec-once`。在合适的地方写入

    ```
    exec-once = mako
    exec-once = /usr/lib/hyprpolkitagent/hyprpolkitagent
    ```

    super+M退出hyprland，重新打开。

## GUI文档管理器

[Xfce4-Projects](https://www.xfce.org/projects)

最适合`xdg-desktop-poratal-gtk`的文档管理器是thunar，内存占用极低，仅50MB。

```
sudo pacman -S xdg-desktop-portal-gtk thunar tumbler ffmpegthumbnailer poppler-glib gvfs-smb file-roller thunar-archive-plugin  gnome-keyring
```

出选项的话选`pipewire-jack`

>`tumbler`提供图片预览功能

>`ffmpegthumbnailer`视频预览

>`poppler-glib`pdf预览

>`gvfs-smb` 检查可挂载的外部设备，访问smb分享等功能

>`file-roller` 提供压缩解压缩功能

>`thunar-archive-plugin`在thunar的右键菜单添加压缩解压缩选项

>`gnome-keyring`提供密码保存功能。第一次保存密码会让你设置keyring的密码，可以空着。

>`icoextract`exe缩略图

更多thunar的额外功能可以看[archwiki的thunar页面](https://wiki.archlinux.org/title/Thunar)

- 设置快捷键

   ```
   vim ~/.config/hypr/hyprland.conf
   ```
   搜索`dolphin`，改成`thunar`。默认快捷键是mainMod+E


### 右键从此处打开终端

点击左上角编辑 > 配置自定义动作 > 选中open in terminal here > 点击小齿轮 > 命令改成`kitty`

### 更改默认终端为kitty


安装`xdg-terminal-exec`

```
yay -S xdg-terminal-exec
```

编辑`~/.config/xdg-terminals.list`

```
echo "kitty.desktop" > ~/.config/xdg-terminals.list
```
如果有多个终端的话就按照顺序写多行。

### 生成home目录下的目录

[Archwiki-XDG-user-directories](https://wiki.archlinux.org/title/XDG_user_directories)

```
sudo pacman -S xdg-user-dirs
LANGUAGE=en_US.UTF-8 xdg-user-dirs-update --force
```

>`LANG=en_US.UTF-8`会生成英文的目录，方便终端中使用。

>`--force`如果你已经有了中文的目录，这个选项会强制覆盖。

## 桌面套件

推荐使用quickshell，dms。

```
yay -S dms-shell
```

在hyprland配置文件中设置自动启动

```
exec-once = dms run
```

### 程序启动器

把配置文件中的`hyprlauncher`改成`dms ipc call launcher toggle`

## 蓝牙

[ArchWiki-Bluetooth](https://wiki.archlinux.org/title/Bluetooth)

```
sudo pacman -S --needed bluez
```
```
sudo systemctl enable --now bluetooth
```
## 锁屏

设置快捷键：

```
bind = $mainMod, L, exec, dms ipc call lock lock
```

## 剪贴板

```
sudo pacman -S wl-clipboard cliphist
```

## 截图

```
yay -S satty slurp grim
```
- 截图仅保存到剪贴板：

  ```
  bind = $mainMod, A, exec, grim -g "$(slurp)" - | wl-copy
  ```

- 编辑截图

  ```
  bind = $mainMod, A, exec, grim -g "$(slurp)" - | satty -f - 
  ```

- 设置satty以浮动模式打开

   打开satty窗口后
   
   ```
   hyprctl clients
   ```
   ```
   windowrule {
   	# 这个窗口规则的名字
   	name = satty
   	# 应用的窗口
   	match:class = com.gabm.satty
   	# 浮动
   	float = yes 
   }
   ```
- 设置satty的复制命令为`wl-copy`
  
   ```
   mkdir -p ~/.config/satty
   vim ~/.config/satty/config.toml
   ```
   
   ```
   [general]
   copy-command = "wl-copy"
   focus-toggles-toolbars= true
   actions-on-right-click = ["save-to-clipboard"]
   ```

## 中文输入法

0. 需要[添加archlinuxcn](安装桌面环境前的准备#AUR助手)

1. 安装

   看中文输入法一节：[中文输入法](中文输入法)

2. 环境变量

   ```
   env = XMODIFIERS,@im=fcitx
   ```

3. 现在启动

   ```
   fcitx5 -d
   ```

4. 自动启动

   ```
   exec-once = fcitx5 -d
   ```

## flatpak

在[安装桌面环境前的准备](安装桌面环境前的准备)一节中我们已经配置了flatpak，可以安装一个GUI。

```
sudo pacman -S bazaar
```
## 下一节：[软件安装相关](软件安装相关)