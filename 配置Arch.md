- [执行顺序说明](#执行顺序说明)
- [A段：基础配置](#a段基础配置)
- [B段：显卡驱动](#b段显卡驱动)
- [C段：中文输入法](#c段中文输入法)
- [D段：显示管理器 DMS](#d段显示管理器-dms)

---

## 执行顺序说明

本文件分为四段，按依赖关系排列：

```
A段（基础配置） ────── 装完系统后立刻做
    ↓
B段（显卡驱动） ────── 可选，按需阅读
    ↓
安装 Niri（详见 安装Niri.md）
    ↓
C段（输入法） ──────── 需要 niri 配置文件的环境变量
D段（DMS） ─────────── 需要 niri 的 .desktop 文件
```

**建议流程**：
1. 从 [安装ArchLinux（Win双系统）](安装ArchLinux（Win双系统）.md) 重启进入新系统
2. 完成本文件的 **A段** 和 **B段**
3. 安装 [Niri](安装Niri.md)
4. 最后回来完成 **C段** 和 **D段**

---

## A段：基础配置

### 设置默认编辑器

系统级的环境变量，很多程序会读取 `EDITOR` 来决定用什么编辑器打开文件：

```
sudo vim /etc/environment
```

写入：

```
EDITOR=vim
```

>如果用 neovim 则填 `nvim`，nano 填 `nano`

需要重新登录后生效：

```
exit
```

### 开启 32 位源

Steam 和 Wine 等需要 32 位库：

```
sudo vim /etc/pacman.conf
```

找到 `[multilib]`，去掉下面两行的注释：

```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

同步数据库：

```
sudo pacman -Syu
```

### 添加 archlinuxcn 源

archlinuxcn 是由社区维护的软件仓库，包含很多官方源没有的包。

```
sudo vim /etc/pacman.conf
```

文件底部追加：

```
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

>中科大和清华的源在国内速度很快。海外用户直接用官方源：`Server = https://repo.archlinuxcn.org/$arch`

同步并安装密钥：

```
sudo pacman -Syu archlinuxcn-keyring
```

### 安装 AUR 助手

AUR（Arch User Repository）是 Arch 最强大的软件仓库，AUR 助手可以一键从 AUR 安装软件。

```
sudo pacman -S --needed base-devel yay paru
```

| 包名 | 说明 |
|------|------|
| `base-devel` | 编译 AUR 包必需的工具链 |
| `yay` | 最流行的 AUR 助手 |
| `paru` | 另一个 AUR 助手，某些 yay 安装失败的包可以换 paru |

使用方式：

```
yay -S 包名
paru -S 包名
```

>两个都装，互相备份。日常使用一个即可。

### 安装字体

```
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji ttf-jetbrains-mono-nerd
```

| 包名 | 说明 |
|------|------|
| `noto-fonts` | 大部分外文字体 |
| `noto-fonts-cjk` | 中日韩字体（注意需要正确配置 fontconfig，否则中文可能显示为日文字形） |
| `noto-fonts-emoji` | Emoji 支持 |
| `ttf-jetbrains-mono-nerd` | 等宽字体，带 Nerd Font 图标，终端和 waybar 常用 |

### 安装音视频服务

PipeWire 是 Red Hat 主导开发的现代音视频服务：

```
sudo pacman -S --needed pipewire wireplumber pipewire-pulse pipewire-alsa pipewire-jack
```

| 包名 | 说明 |
|------|------|
| `pipewire` | 核心音视频服务 |
| `wireplumber` | PipeWire 的智能会话管理 |
| `pipewire-pulse` | PulseAudio 兼容层 |
| `pipewire-alsa` | ALSA 兼容层 |
| `pipewire-jack` | JACK 兼容层 |

启用服务：

```
systemctl --user enable --now pipewire pipewire-pulse wireplumber
```

可选：安装音视频固件（部分笔记本需要）：

```
sudo pacman -S --needed sof-firmware alsa-ucm-conf
```

### 安装蓝牙

```
sudo pacman -S --needed bluez
```

启用服务：

```
sudo systemctl enable --now bluetooth
```

蓝牙管理工具推荐 `bluetui`：

```
sudo pacman -S bluetui
```

### 安装性能模式切换

```
sudo pacman -S power-profiles-daemon
sudo systemctl enable --now power-profiles-daemon
```

提供三个档位：`performance`（性能）、`balanced`（平衡）、`power-saver`（省电）。

切换模式：

```
powerprofilesctl set performance
powerprofilesctl set balanced
powerprofilesctl set power-saver
```

---

## B段：显卡驱动

根据你的显卡选择对应章节。Intel 用户按需阅读 AMD 和 Nvidia 部分即可。

### Intel（你的配置）

```
sudo pacman -S mesa vulkan-intel libva-intel-driver intel-media-driver
```

| 包名 | 说明 |
|------|------|
| `mesa` | 开源图形驱动（Mesa3D） |
| `vulkan-intel` | Intel Vulkan 支持 |
| `libva-intel-driver` | Intel 视频编解码（旧款，VA-API） |
| `intel-media-driver` | Intel 视频编解码（新款，Media Driver），与上一项二选一或都装 |

验证驱动是否正常工作：

```
sudo pacman -S glxinfo
glxinfo | grep "OpenGL renderer"
```

应该输出 `Intel` 相关字样。

### AMD

```
sudo pacman -S mesa vulkan-radeon libva-mesa-driver
```

| 包名 | 说明 |
|------|------|
| `mesa` | 开源图形驱动 |
| `vulkan-radeon` | AMD Vulkan 支持（也可以装 `vulkan-amdgpu-pro` AUR 版） |
| `libva-mesa-driver` | AMD 视频编解码 |

### Nvidia

>Nvidia 在 Wayland 下的支持已经相当成熟，推荐使用开源内核模块。

```
sudo pacman -S nvidia-dkms nvidia-utils libva-nvidia-driver
```

| 包名 | 说明 |
|------|------|
| `nvidia-dkms` | Nvidia 闭源驱动（dkms 版，内核更新时自动重编译） |
| `nvidia-utils` | Nvidia 工具和库（含 `nvidia-smi`） |
| `libva-nvidia-driver` | Nvidia 视频编解码（VA-API 转译） |

>如果是 RTX 20 及更新架构，也可以选择 `nvidia-open-dkms`（开源内核模块），性能和闭源版一致。

在 `/etc/default/grub` 的 `GRUB_CMDLINE_LINUX_DEFAULT` 中添加：

```
nvidia_drm.modeset=1
```

重新生成 GRUB 配置：

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

在 niri 的 `environment { }` 中添加：

```
GDK_BACKEND "wayland"
CLUTTER_BACKEND "wayland"
```

>部分 Electron 应用需要 `--ozone-platform=wayland` 启动参数才能正确使用 Wayland。

---

## C段：中文输入法

### 前提

- 已安装 [Niri](安装Niri.md)
- niri 配置文件中已创建 `environment { }` 代码块

### 安装 fcitx5 框架

```
sudo pacman -S fcitx5-im
```

>`fcitx5-im` 是一个元包，包含 fcitx5 主程序、配置工具、QT/GTK 模块等

### 安装 RIME 引擎和雾凇方案

```
sudo pacman -S fcitx5-rime
yay -S rime-ice-git
```

| 包名 | 说明 |
|------|------|
| `fcitx5-rime` | RIME 中州韵输入法引擎 |
| `rime-ice-git` | 雾凇拼音输入方案（含全拼、双拼等） |

### 配置雾凇为默认方案

创建 RIME 用户配置目录：

```
mkdir -p ~/.local/share/fcitx5/rime
```

编辑默认方案配置：

```
vim ~/.local/share/fcitx5/rime/default.custom.yaml
```

写入：

```yaml
patch:
  __include: rime_ice_suggestion:/
```

重启 fcitx5 使配置生效：

```
fcitx5 -r
```

重启后默认就会使用雾凇拼音。

### 在 fcitx5 中添加 RIME

打开 fcitx5 配置工具：

```
fcitx5-configtool
```

在 `输入法` 列表中，把 `中州韵 (rime)` 添加到左侧已启用列表。

### 配置 niri 环境变量

在 `~/.config/niri/config.kdl` 的 `environment { }` 中添加：

```
environment {
    XMODIFIERS "@im=fcitx"
}
```

### 自启动 fcitx5

在 `config.kdl` 的 `spawn-at-startup` 中添加：

```
spawn-at-startup "fcitx5" "-d"
```

### 可选：安装语法模型

雾凇拼音的长句联想效果一般，接入万象的语法模型可以改善。

```
yay -S rime-wanxiang-gram-zh-hans
```

编辑雾凇配置：

```
vim ~/.local/share/fcitx5/rime/rime_ice.custom.yaml
```

写入：

```yaml
patch:
  "grammar/language": wanxiang-lts-zh-hans
```

重启输入法：

```
fcitx5 -r
```

现在试试输入 `苍茫的天涯是我的爱`，效果应该好了很多。

### 可选：设置输入法切换快捷键

在 niri 配置的 `binds { }` 中添加：

```
Mod+F1 { spawn-sh "pkill fcitx5 || fcitx5 -d"; }
```

>输入法偶尔会卡住，这个快捷键可以快速重启输入法

### 可选：美化 fcitx5 主题

下载主题放到 `~/.local/share/fcitx5/themes/`，在 fcitx5 配置工具的 `经典用户界面` 中选择主题。

### 输入法异常处理

如果在某些软件中无法使用输入法，可能原因：

- **LC_CTYPE 问题**：在 niri 配置中已设 `LC_CTYPE=en_US.UTF-8`，此值可能会导致 Steam 无法输入中文。解决办法是在 Steam 的 `.desktop` 文件中添加 `env LC_CTYPE=zh_CN.UTF-8` 前缀。

- **Electron 应用**：需要添加启动参数：

  ```
  --enable-features=UseOzonePlatform --ozone-platform=wayland --enable-wayland-ime
  ```

---

## D段：显示管理器 DMS

### 什么是 DMS？

DMS（DankMaterialShell Greeter）是一个基于 greetd 的图形化登录界面，提供美观的登录体验。它依赖 greetd 作为登录管理后台，由 Quickshell 提供 UI 渲染。

### 安装依赖

```
sudo pacman -S greetd
yay -S quickshell greetd-dms-greeter-git
```

| 包名 | 说明 |
|------|------|
| `greetd` | 登录管理守护进程 |
| `quickshell` | DMS 使用的 UI 渲染框架 |
| `greetd-dms-greeter-git` | DMS 登录界面（AUR） |

### 配置 greetd

1. 编辑 greetd 配置文件：

    ```
    sudo vim /etc/greetd/config.toml
    ```

    写入：

    ```toml
    [terminal]
    vt = 1

    [default_session]
    command = "dms-greeter-session niri"
    user = "greeter"
    ```

    > `dms-greeter-session niri` 表示用 niri 作为登录界面的显示后端

2. 为 dms 创建 niri 专用配置：

    登录界面需要有独立的 niri 配置，比正常桌面简洁得多。

    ```
    sudo tee /etc/greetd/niri.kdl > /dev/null << 'EOF'
    hotkey-overlay {
        skip-at-startup
    }
    environment {
        DMS_RUN_GREETER "1"
    }
    gestures {
        hot-corners {
            off
        }
    }
    layout {
        background-color "#000000"
    }
    EOF
    ```

    >这个配置禁用了快捷键教程、热角等功能，登录界面不需要这些。

### 同步配置

DMS 可以自动同步你的用户主题、壁纸等到登录界面：

```
dms greeter sync
```

>这会把你加入 `greeter` 组，创建 symlink 等。需要退出重新登录后才能完全生效。

验证同步状态：

```
dms greeter status
```

### 启用 greetd 服务

```
sudo systemctl enable greetd
```

### 重启验证

```
reboot
```

重启后应该就能看到 DMS 的图形化登录界面了。

### 登录后启动 niri

DMS 登录后会自动查找系统中的 `.desktop` 文件来确定可用的会话。niri 安装时已自带了 `/usr/share/wayland-sessions/niri.desktop`，登录后会自动启动 `niri-session`。

如果登录后没有进入 niri，检查：

```
ls /usr/share/wayland-sessions/niri.desktop
```

如果文件缺失，手动创建：

```
sudo vim /usr/share/wayland-sessions/niri.desktop
```

```
[Desktop Entry]
Name=Niri
Comment=Niri compositor
Exec=niri-session
Type=Application
```

### DMS 配置参考

DMS 的配置文件位于 `/var/cache/dms-greeter/`：

| 文件 | 作用 |
|------|------|
| `settings.json` | 外观、行为、时钟、天气设置 |
| `session.json` | 壁纸设置 |
| `colors.json` | 配色方案 |

编辑 `settings.json`：

```
sudo vim /var/cache/dms-greeter/settings.json
```

示例：

```json
{
  "use24HourClock": true,
  "showSeconds": false,
  "weatherEnabled": false,
  "currentThemeName": "blue",
  "fontFamily": "Noto Sans CJK SC",
  "cornerRadius": 12,
  "animationSpeed": 2
}
```

>主题名可选：`blue`、`red`、`green`、`purple` 等

壁纸设置（`session.json`）：

```
sudo vim /var/cache/dms-greeter/session.json
```

```json
{
  "wallpaperPath": "/usr/share/backgrounds/default.jpg",
  "wallpaperFillMode": "PreserveAspectCrop"
}
```

### 卸载 DMS

```
sudo systemctl disable greetd
sudo pacman -Rns greetd-dms-greeter-git quickshell greetd
```
