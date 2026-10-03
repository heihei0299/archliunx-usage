- [重要说明](#重要说明)
- [准备工作](#准备工作)
- [手动安装](#手动安装)

---

## 重要说明

### 双系统分区布局

本教程采用以下分区方案：

| 挂载点 | 分区 | 文件系统 | 说明 |
|--------|------|----------|------|
| `/efi` | Windows 现有 EFI 分区 | FAT32 | **不格式化**，仅挂载 |
| `/` | 新建分区 | ext4 | 系统文件 |
| `/home` | 新建分区 | ext4 | 用户数据 |

- Windows 的 EFI 分区通常为 100MB~512MB，**不要格式化它**，否则 Windows 会无法启动
- ext4 是最成熟稳定的 Linux 文件系统，简单可靠
- 使用 zram 替代 swap 交换空间

### ESP/EFI 说明

EFI 系统分区（ESP）是 UEFI 启动必需的专用分区，存放 `.efi` 启动文件，文件系统必须是 FAT32。Windows 安装时已经自动创建了这个分区（通常标记为 `EFI System Partition`）。本教程直接使用它，不做二次格式化。

### GRUB 引导

GRUB 会扫描硬盘上所有系统，自动生成包含 Windows 和 Arch 的启动菜单。安装了 `os-prober` 后 `grub-mkconfig` 会自动检测 Windows 启动项。

---

## 准备工作

1. **关闭 Windows 快速启动**

    Windows 的快速启动会锁定硬盘，导致 Linux 无法正常挂载分区。进 Windows 的 `电源选项` → `选择电源按钮的功能` → 取消勾选 `启用快速启动`。

2. **BIOS 设置**
    - 确认 BIOS 为 UEFI 模式（非 Legacy/CSM）
    - 关闭 Secure Boot（否则 GRUB 可能无法引导）
    - 关闭 Intel Rapid Storage Technology（如果有的话，改为 AHCI）

3. **制作 Arch Linux 启动盘**

    下载 Arch Linux ISO：[https://archlinux.org/download/](https://archlinux.org/download/)

    推荐使用 Ventoy（[https://ventoy.net](https://ventoy.net)），直接把 ISO 复制进 U 盘即可。或者使用 Rufus（Windows）以 DD 模式写入。

---

## 手动安装

参考链接：

[Arch Linux 简明指南](https://arch.icekylin.online/)

[安装指南 - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)

### 调大字体

Live 环境的默认字体很小，先调大方便操作：

```
setfont ter-v32n
```

或者加载更大的字体：

```
setfont -d
```

### 确认是 UEFI 固件

```
cat /sys/firmware/efi/fw_platform_size
```

如果输出 `64`，说明是 64 位 UEFI，符合条件。如果提示 `No such file or directory`，说明是 BIOS 模式，需要回到 BIOS 改为 UEFI。

### 连接网络

有线网通常自动连接。用手机 USB 共享网络也可以。

```
ip a
```

看到有 IPv4 地址（`192.168.x.x` 之类的）说明已连上。再 ping 一下确认网络通畅：

```
ping bilibili.com
```

`Ctrl+C` 中断。

- 使用 iwctl 连接 Wi-Fi

  Live 环境用 `iwd` 组件连接 Wi-Fi：

  ```
  iwctl
  ```

  进入 iwctl 交互界面后：

  ```
  device list                    # 列出无线网卡设备
  station wlan0 scan             # 扫描网络
  station wlan0 get-networks     # 列出扫描到的 Wi-Fi
  station wlan0 connect "Wi-Fi名称"
  ```

  >Wi-Fi 名称不能含中文

  输入密码后 `exit` 退出。

### 确认开启了 NTP

```
timedatectl
```

输出中应该看到：

```
NTP service: active
```

如果未开启：

```
timedatectl set-ntp true
```

### 设置镜像源

安装 `reflector` 工具并自动选择最快的镜像源：

```
pacman -S reflector
```

```
reflector -p https -a 12 -c cn --v --sort rate --save /etc/pacman.d/mirrorlist

# -p https           只选 HTTPS 源
# -a 12              最近 12 小时内更新过的源
# -c cn              限定中国地区（可以加多个国家，如 -c cn,tw,hk）
# --v                显示过程
# --sort rate        按下载速度排序
# --save             保存到 mirrorlist
```

同步软件包数据库：

```
pacman -Sy
```

### 硬盘分区

先查看当前硬盘和分区情况：

```
lsblk -pf
```

这会列出所有硬盘和分区。确认哪块硬盘是你要装 Arch 的（通常是 `nvme0n1` 或 `sda`）。Windows 所在盘会包含一个 `EFI System` 类型的分区。

用 `fdisk` 再看详细信息：

```
fdisk -l /dev/nvme0n1
```

>如果你的是 SATA 固态或机械硬盘，设备名可能是 `/dev/sda`

使用 `cfdisk` 进行分区操作：

```
cfdisk /dev/nvme0n1
```

1. **选择分区表类型**

    如果提示选择，选 `GPT`。

2. **缩小 Windows 分区腾出空间**

    如果 Windows 占满了硬盘，先在 Windows 系统中用 `磁盘管理` 收缩卷（右键 C 盘 → 压缩卷），在硬盘末尾腾出空间。`cfdisk` 里你会看到空闲空间（Free space）。

    >不建议用 `cfdisk` 缩小 Windows 分区，可能损坏数据。用 Windows 自带的磁盘管理最安全。

3. **创建根分区 `/`**

    选中空闲空间 → 左右键选 `[New]` → 输入大小，例如 `100G` → 回车 → 类型选 `Linux filesystem`（默认就是）。

4. **创建 home 分区 `/home`**

    剩下的空闲空间 → `[New]` → 直接回车使用全部剩余空间 → 类型 `Linux filesystem`。

5. **保存**

    `[Write]` → 输入 `yes` → `[Quit]`。

确认分区结果：

```
lsblk -pf
```

你应该会看到类似这样的输出：

```
/dev/nvme0n1p1  EFI 系统分区（不要动它）
/dev/nvme0n1p2  Linux 根分区（ext4）
/dev/nvme0n1p3  Linux home 分区（ext4）
```

### 格式化分区





```
mkfs.fat -32 /dev/nvme0n1p1 # efi分区
mkfs.ext4 /dev/nvme0n1p2    # 根分区
mkfs.ext4 /dev/nvme0n1p3    # home 分区
```

>设备名替换为你实际的设备名（例如 `nvme0n1p2`、`sda2` 等）

### 挂载分区

按顺序挂载：

```
mount /dev/nvme0n1p2 /mnt                     # 挂载根分区，优先挂载mnt分区
mount --mkdir /dev/nvme0n1p3 /mnt/home        # 挂载 home 分区
mount --mkdir /dev/nvme0n1p1 /mnt/efi         # 挂载 EFI 分区（不格式化）
```

> `--mkdir` 会在 `/mnt` 下自动创建 `home` 和 `efi` 目录

复查挂载：

```
fdisk -l
```

### 安装基础系统

```
pacstrap -K /mnt base linux linux-firmware base-devel networkmanager nano  vim sudo grub efibootmgr os-prober exfat-utils intel-ucode
```

| 包名 | 作用 |
|------|------|
| `base` | Arch 核心软件包 |
| `linux` | Linux 内核 |
| `linux-firmware` | 硬件固件 |
| `base-devel` | 编译软件所需工具链 |
| `networkmanager` | 联网服务 |
| `vim` | 文本编辑器 |
| `sudo` | 提权工具 |
| `grub` | 引导程序 |
| `efibootmgr` | UEFI 启动项管理 |
| `os-prober` | 自动检测其他系统（双系统必需） |
| `exfat-utils` | exFAT 支持（检测 Windows 分区时需要） |
| `intel-ucode` | Intel CPU 微码更新 |

>AMD CPU 用户把 `intel-ucode` 换成 `amd-ucode`

#### 关于 vim

不会用 vim 的话可以用 `nano`：

```
pacstrap -K /mnt base linux linux-firmware base-devel networkmanager nano sudo grub efibootmgr os-prober exfat-utils intel-ucode
```

>nano 基本操作：`Ctrl+S` 保存，`Ctrl+X` 退出，`Ctrl+F` 搜索

### 生成 fstab

系统根据 fstab 自动挂载分区：

```
genfstab -U /mnt > /mnt/etc/fstab
```

查看生成的结果确认正确：

```
cat /mnt/etc/fstab
```

### 切换根目录

```
arch-chroot /mnt
```

此时你的终端提示符会发生变化，现在你就在新系统里操作了。

### 设置时区

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

或者：

```
timedatectl set-timezone Asia/Shanghai
```

生成时间相关配置：

```
hwclock --systohc
```

### 本地化设置

1. 编辑 locale.gen

    ```
    vim /etc/locale.gen
    ```

    搜索找到下面两行，去掉行首的 `#` 注释：

    ```
    en_US.UTF-8 UTF-8
    zh_CN.UTF-8 UTF-8
    ```

    保存退出。

2. 生成本地化配置

    ```
    locale-gen
    ```

3. 设置系统语言

    ```
    vim /etc/locale.conf
    ```

    写入：

    ```
    LANG=en_US.UTF-8
    ```

    >系统语言设为英文，安装完桌面环境后再切换到中文。如果在 `locale.conf` 直接写 `zh_CN.UTF-8`，终端 tty 会显示中文豆腐块。

### 设置主机名

```
vim /etc/hostname
```

写入你喜欢的主机名，例如 `archlinux`：

```
archlinux
```

编辑 hosts 文件：

```
vim /etc/hosts
```

写入：

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain   archlinux
```

>把 `archlinux` 替换为你设置的主机名

### 设置 root 密码

```
passwd
```

输入两遍密码（输入时屏幕没有显示，正常输入即可）。

### 创建普通用户

很多软件拒绝在 root 下运行，普通用户是必须的。

```
useradd -mG wheel 你的用户名
passwd 你的用户名
```

> `-m` 创建 home 目录，`-G wheel` 加入 wheel 组

给 wheel 组 sudo 权限：

```
visudo
```

搜索找到下面这行，去掉注释：

```
%wheel ALL=(ALL:ALL) ALL
```

>visudo 本质也是 vim，操作方式相同

### 安装 GRUB 引导程序

1. 安装 GRUB 到 EFI 分区

    ```
    grub-install --target=x86_64-efi --efi-directory=/efi  --boot-directory=/efi  --bootloader-id=ARCH
    ```

    | 参数 | 说明 |
    |------|------|
    | `--target=x86_64-efi` | 指定 64 位 UEFI 架构 |
    | `--efi-directory=/efi` | EFI 分区的挂载点 |
    | `--bootloader-id=ARCH` | 启动项名称，可自定义 |

2. 编辑 GRUB 配置

    ```
    vim /etc/default/grub
    ```

    修改以下几项：

    - **启动项记忆**

      把 `GRUB_DEFAULT=0` 改为 `=saved`，取消 `GRUB_SAVEDEFAULT=true` 的注释。这样上次选中的启动项会被记住。

    - **显示开机日志**

      `GRUB_CMDLINE_LINUX_DEFAULT` 中去掉 `quiet`，改为：

      ```
      GRUB_CMDLINE_LINUX_DEFAULT="loglevel=5 "
      ```


    - **开启 os-prober 检测 Windows**

      取消最后一行的注释：

      ```
      GRUB_DISABLE_OS_PROBER=false
      ```


3. 生成 GRUB 配置文件



   ```bash
ln -s /efi/grub/grub.cfg   /boot/grub/grub.cfg     #一些主板会去/boot 文件下找grub的配置文件

  ``` 

    ```
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

    输出中应该看到类似 `Found Windows Boot Manager on /dev/nvme0n1p1`，说明成功检测到 Windows。

    如果没检测到 Windows，检查：

    - 是否安装了 `os-prober` 和 `exfat-utils`
    - 是否在 `/etc/default/grub` 中设置了 `GRUB_DISABLE_OS_PROBER=false`


3. 由于之前在 grub 配置中禁用了 zswap，zswap 的修改需要重新生成 grub 配置：

    ```
    grub-mkconfig -o /boot/grub/grub.cfg
    ```


### 退出 chroot 并重启

```
exit
```

```
reboot
```

### 拔掉 U 盘

重启时拔掉启动盘，防止再次进入 Live 环境。

### 选择启动项

如果 BIOS 没有自动进入 GRUB，手动选择 `ARCH` 启动项。

### 首次启动

1. 登录 root 账户

### 启用 NetworkManager

```
systemctl enable --now  NetworkManager
```

>如果之后想用 `iwd` 作为 NetworkManager 的无线后端，可以在安装完成后再配置


2. 连接 Wi-Fi

    ```
    nmtui
    ```

    选择 `Activate a connection` → 选择你的 Wi-Fi → 输入密码。

3. 验证网络

    ```
    ping bilibili.com
    ```

4. 可选：同步双系统时间

    ```
    timedatectl set-local-rtc 1
    ```

    >Windows 使用本地时间，Linux 使用 UTC 时间，双系统共用硬件时钟会导致时间差 8 小时。这条命令让 Linux 也使用本地时间，解决这个冲突。

5. 重启确认 GRUB 菜单

    ```
    reboot
    ```

    此时应该能看到 GRUB 菜单，包含 `Arch Linux` 和 `Windows Boot Manager` 两个选项。

### 放松一下吧

```
pacman -S fastfetch lolcat cmatrix
```

```
fastfetch
```

下一节：[配置Arch](配置Arch.md)
