- [什么是 Niri？](#什么是-niri)
- [安装](#安装)
- [基础配置](#基础配置)
- [显示器配置](#显示器配置)
- [重要组件](#重要组件)
- [面板（Waybar）](#面板waybar)
- [壁纸](#壁纸)
- [锁屏](#锁屏)
- [自动熄屏锁屏睡眠](#自动熄屏锁屏睡眠)
- [截图](#截图)
- [剪贴板](#剪贴板)
- [输入法](#输入法)
- [屏幕分享](#屏幕分享)
- [窗口美化](#窗口美化)
- [niri 参考配置](#niri-参考配置)

---

## 什么是 Niri？

Niri 是一个基于 Wayland 的滚动平铺窗口管理器，核心特点是**横向无限卷轴式布局**。不像传统桌面那样窗口只能在一个屏幕范围内排列，Niri 的工作区可以向左右无限延伸。

更多特性建议看官方的 Demo 视频：[Niri 演示视频](https://github.com/YaLTeR/niri)

---

## 安装

### 前提

- 已[安装 Arch Linux](安装ArchLinux（Win双系统）.md) 并启动了系统
- 已[配置 archlinuxcn 源和 AUR 助手](配置Arch.md)（`yay` / `paru`）

### 安装 niri

```
sudo pacman -S niri xwayland-satellite xdg-desktop-portal-gnome fuzzel kitty
```

| 包名 | 作用 |
|------|------|
| `niri` | niri 窗口管理器本体 |
| `xwayland-satellite` | 在 Wayland 上运行 X11 应用的兼容层 |
| `xdg-desktop-portal-gnome` | 提供文件选择、屏幕分享等桌面门户功能 |
| `fuzzel` | 应用启动器，niri 默认快捷键 `Super+D` 调用 |
| `kitty` | 终端模拟器 |

### 第一次运行

```
niri-session
```

>这会在一个纯文本 tty 中启动 niri 会话，自动生成默认配置文件 `~/.config/niri/config.kdl`

`Super+Shift+E` 退出 niri。

> `Super` 键就是 `Win` 键

### 基础使用方法

| 快捷键 | 功能 |
|--------|------|
| `Super+Shift+/` | 快捷键教程菜单 |
| `Super+T` | 打开终端 |
| `Super+D` | 打开应用启动器 |
| `Super+Q` | 关闭当前窗口 |
| `Super+U` / `Super+I` | 上下切换工作区 |
| `Super+J` / `Super+K` | 左右切换窗口 |
| `Super+Shift+J` / `Super+Shift+K` | 左右移动窗口 |
| `Super+F` | 全屏当前窗口 |
| `Super+Shift+F` | 切换窗口浮动模式 |
| `Super+O` | 打开 Overview 视图 |

---

## 基础配置

### 修改默认终端

编辑 niri 配置文件：

```
vim ~/.config/niri/config.kdl
```

搜索 `alacritty`，找到：

```
Mod+T hotkey-overlay-title="Open a Terminal: alacritty" { spawn "alacritty"; }
```

改成：

```
Mod+T hotkey-overlay-title="Open a Terminal: kitty" { spawn "kitty"; }
```

>如果你想在 kitty 中默认启动 fish shell：
>
>```
>Mod+T hotkey-overlay-title="Open a Terminal: kitty" { spawn "kitty" "-e" "fish"; }
>```
>
>或者用 `spawn-sh` 写法（更方便但有小性能开销）：
>
>```
>Mod+T hotkey-overlay-title="Open a Terminal: kitty" { spawn-sh "kitty -e fish"; }
>```

### 设置系统语言为中文

在 `config.kdl` 中找到 `environment { }` 代码块，填入：

```
environment {
    LANG "zh_CN.UTF-8"
    LC_CTYPE "en_US.UTF-8"
}
```

>`LANG` 设为中文，`LC_CTYPE` 设为英文可以解决输入法漏字问题

>如果 `locale.gen` 中之前没有生成 `zh_CN.UTF-8`，需要先：
>
>```
>sudo vim /etc/locale.gen
>```
>
>取消 `zh_CN.UTF-8 UTF-8` 的注释，然后运行：
>
>```
>sudo locale-gen
>```

---

## 显示器配置

### 获取显示器信息

```
niri msg outputs
```

输出类似：

```
Output "BOE NE156QHM-NY1 Unknown" (eDP-1)
  Current mode: 2560x1440 @ 165.000 Hz
  Scale: 1.3333333333333333
  …
Output "Shenzhen KTC Technology Group H27T22C" (DP-2)
  Current mode: 2560x1440 @ 180.000 Hz
  Scale: 1
  …
```

记住 `eDP-1`、`DP-2` 这类名称和可用的分辨率。

### 配置 output

在 `config.kdl` 中写 `output {}` 块：

```
output "eDP-1" {
    mode "2560x1440@165"
    scale 1.33
    position x=0 y=0
    focus-at-startup
}

output "DP-2" {
    mode "2560x1440@180"
    scale 1
    position x=1925 y=0
}
```

> `position x=1925` 的计算方式：eDP-1 分辨率 2560，缩放 1.33，实际逻辑宽度为 `2560 / 1.33 ≈ 1925`，所以 DP-2 的 x 从 1925 开始

### 多显示器位置计算

把最左边或最左上角的显示器设为 `x=0 y=0`，以此为原点计算其他显示器的位置。纵坐标同理，想放在下面就需要修改 y 值。

---

## 重要组件

### 通知服务

```
sudo pacman -S mako libnotify
```

在 `config.kdl` 中添加自启动：

```
spawn-at-startup "mako"
```

编辑 mako 配置：

```
mkdir -p ~/.config/mako
vim ~/.config/mako/config
```

```
default-timeout=5000
border-radius=8
```

重新加载：

```
makoctl reload
```

### 认证代理

很多需要管理员权限的程序通过 polkit 询问密码。

```
sudo pacman -S polkit-gnome
```

自启动：

```
spawn-at-startup "/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1"
```

---

## 面板（Waybar）

### 安装

```
sudo pacman -S waybar ttf-jetbrains-mono-nerd
```

>`ttf-jetbrains-mono-nerd` 提供图标字体

### 自启动

```
spawn-at-startup "waybar"
```

### 重启 waybar 的快捷键

在 `binds { }` 中新增：

```
Mod+F12 { spawn-sh "pkill waybar || true && waybar"; }
```

### waybar 配置

waybar 的配置文件在 `~/.config/waybar/`，包含 `style.css`（样式）和 `config.jsonc`（布局）。这里给一份基础配置：

```
mkdir -p ~/.config/waybar
```

`~/.config/waybar/config.jsonc`：

```json
{
    "layer": "top",
    "position": "bottom",
    "modules-left": ["hyprland/workspaces"],
    "modules-center": ["clock"],
    "modules-right": ["pulseaudio", "network", "bluetooth", "cpu", "memory", "tray"],
    "clock": {
        "format": "{:%Y-%m-%d %H:%M}",
        "tooltip": false
    },
    "pulseaudio": {
        "format": "{icon} {volume}%",
        "format-icons": ["", "", ""]
    },
    "network": {
        "format-wifi": " {essid}",
        "format-ethernet": " {ipaddr}",
        "format-disconnected": "⚠ Disconnected"
    },
    "cpu": {
        "format": " {usage}%"
    },
    "memory": {
        "format": " {}%"
    }
}
```

重启 waybar 生效：`Mod+F12`。

---

## 壁纸

### 安装

```
sudo pacman -S awww
yay -S waypaper
```

>`awww` 是壁纸守护进程，`waypaper` 是图形化壁纸管理工具

### 配置

自启动壁纸守护进程：

```
spawn-at-startup "awww-daemon"
```

设置壁纸切换快捷键：

```
Mod+Alt+W { spawn "waypaper"; }
```

### Overview 壁纸

Niri 的 Overview（`Super+O`）默认灰色背景。可以单独设置壁纸：

1. 启动一个专用于 overview 的 awww 守护进程：

    ```
    awww-daemon -n overview
    ```

    自启动：

    ```
    spawn-at-startup "awww-daemon" "-n" "overview"
    ```

2. 在 `config.kdl` 中添加 layer 规则：

    ```
    layer-rule {
        match namespace="awww-daemonoverview"
        place-within-backdrop true
    }
    ```

3. 设置 overview 壁纸：

    ```
    awww img /path/to/wallpaper.png -n overview
    ```

---

## 锁屏

### 安装

```
yay -S swaylock-effects
```

创建配置：

```
mkdir -p ~/.config/swaylock
vim ~/.config/swaylock/config
```

```
screenshots
clock
indicator
indicator-radius=200
indicator-thickness=15
effect-blur=10x5
font=Noto Sans CJK SC
```

niri 默认配置了 `Mod+Alt+L` 为锁屏快捷键。

---

## 自动熄屏锁屏睡眠

### 安装

```
sudo pacman -S swayidle
```

### 创建脚本

```
mkdir -p ~/.config/niri/scripts
vim ~/.config/niri/scripts/swayidle.sh
```

```bash
#!/usr/bin/env bash

# 5 分钟锁屏，10 分钟熄屏，20 分钟睡眠

swayidle -w \
    timeout 300  'swaylock -f' \
    timeout 600  'niri msg action power-off-monitors' \
    resume       'niri msg action power-on-monitors' \
    timeout 1200 'systemctl suspend' \
```

添加可执行权限：

```
chmod +x ~/.config/niri/scripts/swayidle.sh
```

自启动：

```
spawn-at-startup "~/.config/niri/scripts/swayidle.sh"
```

---

## 截图

Niri 自带截图功能，在配置文件中搜索 `screenshot` 可以找到键位。

默认快捷键：

| 快捷键 | 功能 |
|--------|------|
| `Print` | 全屏截图并保存 |
| `Shift+Print` | 选取区域截图 |
| `Alt+Print` | 聚焦窗口截图 |

`screenshot-path` 可以设置保存位置：

```
screenshot-path "~/Pictures/Screenshots/screenshot_%Y-%m-%d_%H-%M-%S.png"
```

### 编辑截图

```
sudo pacman -S satty
```

设置快捷键调用 satty 编辑剪贴板中的截图：

```
Mod+Shift+S { spawn-sh "wl-paste | satty -f -"; }
```

先按截图快捷键（任意方式），再按 `Mod+Shift+S` 把截图送入 satty 编辑。

---

## 剪贴板

```
sudo pacman -S wl-clipboard
yay -S clipse clipse-gui
```

>`wl-clipboard` 是 Wayland 剪贴板基础工具
>`clipse` 是剪贴板历史管理工具

自启动 clipse 守护进程：

```
spawn-at-startup "clipse" "--listen"
```

设置快捷键打开 clipse-gui：

```
Mod+Alt+V { spawn "clipse-gui"; }
```

以浮动模式打开 clipse-gui：

```
window-rule {
    match app-id="clipse-gui"
    open-floating true
}
```

---

## 输入法

输入法的安装步骤在[配置Arch](配置Arch.md)中，这里只配置 niri 中的环境变量和自启动。

在 `config.kdl` 的 `environment { }` 中添加：

```
XMODIFIERS "@im=fcitx"
```

自启动 fcitx5：

```
spawn-at-startup "fcitx5" "-d"
```

---

## 屏幕分享

录屏推荐使用 OBS Studio 或 Kooha。Niri 通过 GNOME 桌面门户提供屏幕分享，已经安装了 `xdg-desktop-portal-gnome`，通常可以直接使用。

如果无法使用，在 `spawn-at-startup` 中添加：

```
spawn-at-startup "dbus-update-activation-environment" "--systemd" "WAYLAND_DISPLAY" "XDG_CURRENT_DESKTOP=niri"
```

---

## 窗口美化

### 全局圆角

搜索 `geometry-corner-radius`，找到被注释掉的示例，取消注释：

```
window-rule {
    geometry-corner-radius 8
    clip-to-geometry true
    opacity 0.99
    draw-border-with-background false
}
```

### 边框颜色

搜索 `focus-ring` 调整：

```
focus-ring {
    active-color "#999999ff"
    inactive-color "#444444ff"
    width 1
}
```

### 隐藏窗口标题栏

```
prefer-no-csd
```

>部分应用可能不遵循此设置

### 光标主题

```
sudo pacman -S breeze-cursors
```

```
cursor {
    xcursor-theme "breeze_cursors"
    xcursor-size 30
    hide-after-inactive-ms 15000
}
```

### 聚焦跟随鼠标

搜索 `focus-follows-mouse`：

```
focus-follows-mouse
```

配合 `warp-mouse-to-focus` 自动移动鼠标到聚焦窗口。

### 窗口模糊（Blur）

Niri 自 26.04 版本开始支持模糊效果：

```
blur {
    passes 3
    offset 3
    noise 0.02
    saturation 1.5
}

window-rule {
    background-effect {
        xray true
        blur true
    }
}
```

>`xray` 只渲染一次模糊背景然后固定显示，无性能消耗

---

## niri 参考配置

完整的 `~/.config/niri/config.kdl` 结构概览：

```
// 环境变量
environment {
    LANG "zh_CN.UTF-8"
    LC_CTYPE "en_US.UTF-8"
    XMODIFIERS "@im=fcitx"
}

// 输入
input {
    mouse {
        accel-profile "flat"
    }
}

// 光标
cursor {
    xcursor-theme "breeze_cursors"
    xcursor-size 30
}

// 显示器
output "eDP-1" {
    mode "2560x1440@165"
    scale 1.33
    position x=0 y=0
    focus-at-startup
}

// 布局
layout {
    gap 8
}

// 窗口圆角
prefer-no-csd
focus-ring {
    active-color "#999999ff"
    inactive-color "#444444ff"
    width 1
}

// 窗口规则
window-rule {
    geometry-corner-radius 8
    clip-to-geometry true
    opacity 0.99
    draw-border-with-background false
}

// 自启动
spawn-at-startup "waybar"
spawn-at-startup "mako"
spawn-at-startup "/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1"
spawn-at-startup "fcitx5" "-d"
spawn-at-startup "awww-daemon"
spawn-at-startup "clipse" "--listen"
spawn-at-startup "~/.config/niri/scripts/swayidle.sh"

// 快捷键
binds {
    Mod+T hotkey-overlay-title="Open a Terminal: kitty" { spawn "kitty"; }
    Mod+D hotkey-overlay-title="Application Launcher" { spawn "fuzzel"; }
    Mod+Q hotkey-overlay-title="Close Window" { close-window; }
    Mod+F12 { spawn-sh "pkill waybar || true && waybar"; }
    Mod+Alt+L { spawn "swaylock"; }
    Mod+Alt+V { spawn "clipse-gui"; }
    Mod+Shift+S { spawn-sh "wl-paste | satty -f -"; }
    Mod+Alt+W { spawn "waypaper"; }
}

// 截图
screenshot-path "~/Pictures/Screenshots/screenshot_%Y-%m-%d_%H-%M-%S.png"
```

---

下一节：[配置Arch](配置Arch.md)
