- [什么是 Niri？](#什么是-niri)
- [安装](#安装)
  - [前提](#前提)
  - [安装 niri](#安装-niri)
  - [第一次运行](#第一次运行)
  - [基础使用方法](#基础使用方法)
- [安装 DMS 桌面 Shell](#安装-dms-桌面-shell)

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

## 安装 DMS 桌面 Shell

DankMaterialShell 是一套完整的桌面 Shell，适用于 niri、Hyprland、Sway 等 Wayland 合成器。它取代了 waybar、swaylock、swayidle、mako、fuzzel、polkit 等组件。

使用一键安装脚本自动配置，桌面选择 `niri`，终端选择 `kitty`：

```
curl -fsSL https://install.danklinux.com | sh
```

> 详细配置（登录管理器 DMS 等）见 [配置Arch.md](配置Arch.md) 的 D 段。

