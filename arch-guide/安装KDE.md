
[KDE-Archwiki](https://wiki.archlinux.org/title/KDE)

1. 安装

   ```
   sudo pacman -S plasma-meta konsole dolphin flatpak-kcm kate firefox qt6-multimedia-ffmpeg 
   ```

   >`plasma-meta` 但是kde自带的东西都很实用，装meta包就可以了

   >`konsole` 是kde标配终端仿真器

   >`dolphin` 是kde标配文档管理器

   >`flatpak-kcm` 通过系统设置管理flatpak应用的权限

   >`kate` 是标配文本编辑器

   >`firefox`是linux上最好用的浏览器

   >`qt6-multimedia-ffmpeg`多媒体相关依赖

2. 开启显示管理器

   ```
   sudo systemctl start plasmalogin
   ```

   自6.5版本开始，plasma同时提供`sddm`和`plasmalogin`，推荐使用`plasmalogin`。

3. 登录普通用户

4. 设置开机自启

   成功开启桌面后打开konsole设置显示管理器开机自启

   ```
   sudo systemctl enable plasmalogin 
   ```

### 设置系统语言

- 系统设置 > regin&language > language边上的modify > 点击add more添加简体中文 > 移动到english上方

- 如果是archinstall安装，需要进行如下操作：

  1. ```
     sudo vim /etc/locale.gen 
     ```

  2. 左斜杠键搜索，取消`zh_CN.UTF-8`的注释

  3. ```
     sudo locale-gen
     ```

  4. 登出

### 生成home下目录（如果没有的话）

```
LANG=en_US.UTF-8 xdg-user-dirs-update --force
```

这段命令将强制生成英文的home下常用目录，方便终端中使用。如果你已经存在中文的home下常用目录请运行这段命令后删除中文的。

### 接下来你自己根据需求决定安装什么软件，进行什么配置。当然，你也可以选择参考我的

## 下一节：[软件安装相关](软件安装相关)
