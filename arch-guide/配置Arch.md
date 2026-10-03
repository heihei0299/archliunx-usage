- [执行顺序说明](#执行顺序说明)
- [A段：基础配置](#a段基础配置)
  - [设置默认编辑器](#设置默认编辑器)
  - [开启 32 位源](#开启-32-位源)
  - [添加 archlinuxcn 源](#添加-archlinuxcn-源)
  - [安装 AUR 助手](#安装-aur-助手)
  - [安装字体](#安装字体)
  - [配置 kitty 终端和 vscode 终端字体](#配置-kitty-终端和-vscode-终端字体)
  - [配置arch字体](#配置arch字体)
  - [安装音视频服务](#安装音视频服务)
  - [安装蓝牙](#安装蓝牙)
  - [安装性能模式切换](#安装性能模式切换)
- [B段：显卡驱动](#b段显卡驱动)
  - [Intel（你的配置）](#intel你的配置)
  - [AMD](#amd)
  - [Nvidia](#nvidia)
- [C段：中文输入法](#c段中文输入法)
  - [前提](#前提)
  - [安装 fcitx5 框架](#安装-fcitx5-框架)
  - [安装 RIME 引擎和雾凇方案](#安装-rime-引擎和雾凇方案)
  - [配置雾凇为默认方案](#配置雾凇为默认方案)
  - [在 fcitx5 中添加 RIME](#在-fcitx5-中添加-rime)
  - [配置 niri 环境变量](#配置-niri-环境变量)
  - [自启动 fcitx5](#自启动-fcitx5)
  - [可选：设置输入法切换快捷键](#可选设置输入法切换快捷键)
  - [可选：美化 fcitx5 主题](#可选美化-fcitx5-主题)
  - [输入法异常处理](#输入法异常处理)
- [D段：显示管理器 DMS](#d段显示管理器-dms)
  - [什么是 dms-greeter？](#什么是-dms-greeter)
  - [安装依赖](#安装依赖)
    - [方式一：自动安装（推荐）](#方式一自动安装推荐)
    - [方式二：手动安装](#方式二手动安装)
  - [启用欢迎界面](#启用欢迎界面)
  - [与用户主题同步](#与用户主题同步)
  - [检查安装情况](#检查安装情况)
  - [配置 greetd](#配置-greetd)
  - [再次检查安装情况](#再次检查安装情况)
  - [重启验证](#重启验证)

---

## 执行顺序说明

本文件分为四段，按依赖关系排列：

```
A段（基础配置） ────── 装完系统后立刻做
    ↓
B段（显卡驱动） ────── 可选，按需阅读
    ↓
安装 Niri（详见 [install-niri+dms.md](install-niri+dms.md)）
    ↓
C段（输入法） ──────── 需要 niri 配置文件的环境变量
D段（DMS） ─────────── 需要 niri 的 .desktop 文件
```

**建议流程**：
1. 从 [安装ArchLinux（Win双系统）](安装ArchLinux（Win双系统）.md) 重启进入新系统
2. 完成本文件的 **A段** 和 **B段**
3. 安装 [Niri](install-niri+dms.md)
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
EDITOR=nano
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

| 包名         | 说明                                              |
| ------------ | ------------------------------------------------- |
| `base-devel` | 编译 AUR 包必需的工具链                           |
| `yay`        | 最流行的 AUR 助手                                 |
| `paru`       | 另一个 AUR 助手，某些 yay 安装失败的包可以换 paru |

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

| 包名                      | 说明                                                                  |
| ------------------------- | --------------------------------------------------------------------- |
| `noto-fonts`              | 大部分外文字体                                                        |
| `noto-fonts-cjk`          | 中日韩字体（注意需要正确配置 fontconfig，否则中文可能显示为日文字形） |
| `noto-fonts-emoji`        | Emoji 支持                                                            |
| `ttf-jetbrains-mono-nerd` | 等宽字体，带 Nerd Font 图标，终端和 waybar 常用                       |

### 配置 kitty 终端和 vscode 终端字体

配置 kitty 终端字体，改为 `JetBrainsMono Nerd Font`：

```
nano .config/kitty/kitty.conf
```

写入：

```
family   JetBrainsMono Nerd Font
bold_font auto
bold_italic_font auto
italic_font auto
symbol_map U+4E00-U+9FFF Noto Sans CJK SC
emoji_font Noto Color Emoji
```

配置 vscode 终端字体，修改 `settings.json`，添加下面的配置

```json

{
    "editor.fontFamily": "JetBrainsMono Nerd Font",
    "terminal.integrated.fontFamily": 
"JetBrainsMono Nerd Font Mono, Symbols Nerd Font Mono, Noto Sans CJK SC, Noto Color Emoji",    
    "terminal.integrated.fontLigatures": true,
    "terminal.integrated.gpuAcceleration": "off",
    "chat.agent.enabled": false,
    "chat.checkpoints.enabled": false,
    "chat.viewSessions.enabled": false,
    "workbench.secondarySideBar.defaultVisibility": "hidden"
}

```



### 配置arch字体 


```bash
~/.config/fontconfig/fonts.conf
```

写入👇（最终稳定版）

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>

<!-- ========== monospace 主字体链 ========== -->
<match target="pattern">
  <test name="family">
    <string>monospace</string>
  </test>

  <edit name="family" mode="prepend">
    <string>JetBrainsMono Nerd Font Mono</string>
    <string>Noto Sans Mono CJK SC</string>
  </edit>
</match>

<!-- ========== sans 中文 fallback ========== -->
<match target="pattern">
  <test name="lang" compare="contains">
    <string>zh</string>
  </test>

  <edit name="family" mode="prepend">
    <string>Noto Sans CJK SC</string>
  </edit>
</match>

<!-- ========== emoji ========== -->
<alias>
  <family>emoji</family>
  <prefer>
    <family>Noto Color Emoji</family>
  </prefer>
</alias>

</fontconfig>
```


### 安装音视频服务

PipeWire 是 Red Hat 主导开发的现代音视频服务：

```
sudo pacman -S --needed pipewire wireplumber pipewire-pulse pipewire-alsa pipewire-jack
```

| 包名             | 说明                    |
| ---------------- | ----------------------- |
| `pipewire`       | 核心音视频服务          |
| `wireplumber`    | PipeWire 的智能会话管理 |
| `pipewire-pulse` | PulseAudio 兼容层       |
| `pipewire-alsa`  | ALSA 兼容层             |
| `pipewire-jack`  | JACK 兼容层             |

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

| 包名                 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| `mesa`               | 开源图形驱动（Mesa3D）                                       |
| `vulkan-intel`       | Intel Vulkan 支持                                            |
| `libva-intel-driver` | Intel 视频编解码（旧款，VA-API）                             |
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

| 包名                | 说明                                                   |
| ------------------- | ------------------------------------------------------ |
| `mesa`              | 开源图形驱动                                           |
| `vulkan-radeon`     | AMD Vulkan 支持（也可以装 `vulkan-amdgpu-pro` AUR 版） |
| `libva-mesa-driver` | AMD 视频编解码                                         |

### Nvidia

>Nvidia 在 Wayland 下的支持已经相当成熟，推荐使用开源内核模块。

```
sudo pacman -S nvidia-dkms nvidia-utils libva-nvidia-driver
```

| 包名                  | 说明                                             |
| --------------------- | ------------------------------------------------ |
| `nvidia-dkms`         | Nvidia 闭源驱动（dkms 版，内核更新时自动重编译） |
| `nvidia-utils`        | Nvidia 工具和库（含 `nvidia-smi`）               |
| `libva-nvidia-driver` | Nvidia 视频编解码（VA-API 转译）                 |

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

- 已安装 [Niri](install-niri+dms.md)
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

| 包名           | 说明                               |
| -------------- | ---------------------------------- |
| `fcitx5-rime`  | RIME 中州韵输入法引擎              |
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

### 什么是 dms-greeter？

dms-greeter 是一款 greetd 登录界面程序，采用了与 DankMaterialShell 锁屏界面相同的美学风格。

### 安装依赖

有两种安装方式可选：

#### 方式一：自动安装（推荐）

```
dms greeter install
```

适用于任何发行版（Arch Linux 需要 `paru` 或 `yay`）。配置、权限、主题同步和 greetd 服务启用均自动处理。

#### 方式二：手动安装

```
sudo pacman -S greetd
yay -S quickshell greetd-dms-greeter-git
```

| 包名                     | 说明                   |
| ------------------------ | ---------------------- |
| `greetd`                 | 登录管理守护进程       |
| `quickshell`             | DMS 使用的 UI 渲染框架 |
| `greetd-dms-greeter-git` | DMS 登录界面（AUR）    |

### 启用欢迎界面

```
dms greeter enable
```

这将：
- 配置 `/etc/greetd/config.toml`，使用正确的合成器命令
- 禁用冲突的显示管理器（gdm、lightdm、sddm）
- 启用并启动 greetd 服务

### 与用户主题同步

```
dms greeter sync
```

这将：
- 安装 `acl`（如果尚未安装）
- 将用户添加到 `greeter` 组
- 设置 ACL 权限，以便 greeter 访问配置目录
- 创建符号链接以同步设置、壁纸和颜色主题

### 检查安装情况

```
dms greeter status
```

验证项：
- ✓ 用户组成员身份
- ✓ 缓存目录是否存在
- ✓ 配置符号链接（设置、壁纸、颜色）
- ✓ 源文件是否存在且可读

示例输出：

```
=== DMS Greeter Status ===
Group Membership:
  ✓ User is in greeter group
Greeter Cache Directory:
  ✓ /var/cache/dms-greeter exists
Configuration Symlinks:
  ✓ Settings: synced correctly
  ✓ Session state: synced correctly
  ✓ Color theme: synced correctly
✓ All checks passed! Greeter is properly configured.
```

> 如果出现部分错误，可以重启后再次执行 `dms greeter status` 查看情况。

### 配置 greetd

创建 niri 登录合成器配置：

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

然后在 `/etc/greetd/config.toml` 中修改合成器命令：

```
command = "dms-greeter --command niri -C /etc/greetd/niri.kdl"
```

### 再次检查安装情况

```
dms greeter status
```

验证项：
- ✓ 用户组成员身份
- ✓ 缓存目录是否存在
- ✓ 配置符号链接（设置、壁纸、颜色）
- ✓ 源文件是否存在且可读

示例输出：

```
=== DMS Greeter Status ===
Group Membership:
  ✓ User is in greeter group
Greeter Cache Directory:
  ✓ /var/cache/dms-greeter exists
Configuration Symlinks:
  ✓ Settings: synced correctly
  ✓ Session state: synced correctly
  ✓ Color theme: synced correctly
✓ All checks passed! Greeter is properly configured.
```

### 重启验证

```
reboot
```

重启后应该就能看到 DMS 的图形化登录界面了。
