# Arch Linux Timeshift 完整教程

## 一、什么是 Timeshift

Timeshift 是 Linux 下最流行的系统快照工具之一。

它的作用类似于：

* Windows 系统还原
* macOS Time Machine（仅系统部分）

可以在以下情况快速回滚：

* pacman 更新后系统损坏
* 安装错误驱动导致无法进入桌面
* 修改配置导致系统异常
* 内核升级失败
* 引导器损坏

注意：

Timeshift 主要用于恢复系统，不建议作为个人数据备份工具。

用户文件建议单独使用：

* rsync
* borg
* restic
* Syncthing

---

## 二、安装 Timeshift

### 官方仓库安装

```bash
sudo pacman -S timeshift
```

验证安装：

```bash
timeshift --version
```

---

## 三、查看磁盘布局

```bash
lsblk -f
```

例如：

```text
nvme0n1
├─nvme0n1p1 vfat
├─nvme0n1p2 ext4 /
└─nvme0n1p3 ext4 /home
```

或者：

```text
nvme0n1
├─nvme0n1p1 vfat
└─nvme0n1p2 btrfs
```

---

## 四、选择工作模式

### RSYNC 模式（推荐新手）

适用于：

* ext4
* xfs
* f2fs
* btrfs

特点：

* 通用
* 稳定
* 不依赖文件系统

---

### BTRFS 模式

适用于：

```text
@
@home
```

子卷布局。

特点：

* 创建快照秒级完成
* 占用空间极小
* 恢复速度快

---

## 五、首次配置

启动：

```bash
sudo timeshift-launcher
```

或：

```bash
sudo timeshift-gtk
```

选择：

```text
RSYNC
```

选择快照存储分区：

推荐：

* 独立数据盘
* 大容量 SSD
* 非系统根分区

---

## 六、推荐快照策略

### 个人电脑

Daily

```text
7
```

Weekly

```text
4
```

Monthly

```text
3
```

保留：

```text
最近7天
最近4周
最近3个月
```

---

### 开发环境

Daily

```text
14
```

Weekly

```text
8
```

Monthly

```text
6
```

---

## 七、推荐过滤器

Timeshift → Settings → Filters

排除：

```text
/home/**
/tmp/**
/var/tmp/**
/run/**
/var/run/**
/var/log/**
/var/cache/**
/mnt/**
/media/**
```

如果使用 Docker：

```text
/var/lib/docker/**
```

如果使用 KVM：

```text
/var/lib/libvirt/images/**
```

如果使用 VirtualBox：

```text
/home/*/VirtualBox VMs/**
```

---

## 八、推荐 timeshift.json

文件位置：

```bash
/etc/timeshift/timeshift.json
```

内容：

{
"backup_device_uuid" : "",
"parent_device_uuid" : "",
"do_first_run" : "false",

"btrfs_mode" : "false",
"include_btrfs_home_for_backup" : "false",
"include_btrfs_home_for_restore" : "false",

"stop_cron_emails" : "true",

"schedule_monthly" : "true",
"schedule_weekly" : "true",
"schedule_daily" : "true",
"schedule_hourly" : "false",
"schedule_boot" : "false",

"count_monthly" : "3",
"count_weekly" : "4",
"count_daily" : "7",
"count_hourly" : "0",
"count_boot" : "0",

"snapshot_type" : "RSYNC",

"exclude" : [
"/home/**",
"/tmp/**",
"/var/tmp/**",
"/run/**",
"/var/run/**",
"/var/log/**",
"/var/cache/**",
"/mnt/**",
"/media/**",
"/var/lib/docker/**"
]
}

---

## 九、创建快照

手动创建：

```bash
sudo timeshift --create
```

添加备注：

```bash
sudo timeshift --create \
--comments "Before pacman -Syu"
```

查看快照：

```bash
sudo timeshift --list
```

---

## 十、删除快照

查看：

```bash
sudo timeshift --list
```

删除指定快照：

```bash
sudo timeshift --delete \
--snapshot '2026-06-18_12-00-01'
```

删除所有：

```bash
sudo timeshift --delete-all
```

---

## 十一、系统恢复

### 图形界面恢复

```bash
sudo timeshift-launcher
```

选择快照：

```text
Restore
```

按照向导完成恢复。

---

### 命令行恢复

查看：

```bash
sudo timeshift --list
```

恢复：

```bash
sudo timeshift --restore
```

按照提示选择快照。

---

## 十二、系统无法启动时恢复

准备：

* Arch ISO U盘

启动 Live 环境：

```bash
sudo mount /dev/nvme0n1p2 /mnt
```

进入系统：

```bash
arch-chroot /mnt
```

查看快照：

```bash
timeshift --list
```

恢复：

```bash
timeshift --restore
```

恢复完成后：

```bash
reboot
```

---

## 十三、常用命令

创建快照：

```bash
sudo timeshift --create
```

查看快照：

```bash
sudo timeshift --list
```

恢复：

```bash
sudo timeshift --restore
```

删除快照：

```bash
sudo timeshift --delete
```

删除全部快照：

```bash
sudo timeshift --delete-all
```

检查配置：

```bash
sudo timeshift --check
```

查看配置：

```bash
sudo cat /etc/timeshift/timeshift.json
```

查看版本：

```bash
timeshift --version
```

---

## 十四、最佳实践

更新系统前：

```bash
sudo timeshift --create \
--comments "Before update"
```

安装 NVIDIA 驱动前：

```bash
sudo timeshift --create \
--comments "Before NVIDIA"
```

升级内核前：

```bash
sudo timeshift --create \
--comments "Before kernel upgrade"
```

养成：

```text
升级前快照
出问题恢复
继续工作
```

的习惯。

