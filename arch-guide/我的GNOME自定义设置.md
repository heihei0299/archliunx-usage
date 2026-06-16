目录

- [快捷键设置](GNOME配置#快捷键)
- [功能性扩展](GNOME配置#功能性扩展)
- [其他有用扩展](GNOME配置#其他有用的扩展)
- [美化用扩展](GNOME配置#美化用扩展)
- [实现Windows布局](GNOME配置#实现windows布局)
- [调节外接屏幕亮度](GNOME配置#调节外接屏幕亮度)
- [光标主题](GNOME配置#光标主题)
- [GNOME主题](GNOME配置#gnome主题)
- [导出快捷键](GNOME配置#导出gnome快捷键)
---


## 快捷键

- 可选：交换大写锁定键和esc键

  安装gnome-tweaks

  ```
  sudo pacman -S gnome-tweaks
  ```

  在键盘→其他布局里面交换CAPSLOCK和ESC键



设置>键盘>查看自定义快捷键

* 导航

```
super+ctrl+Q/E #将窗口左右移动工作区
super+shift+Q/E #移动到左/右工作区
ps：gnome默认super+滚轮上下可以左右切换工作区
alt+tap #切换窗口
super+tab #切换应用程序
alt+` #在应用程序的窗口之间切换窗口
super+H #隐藏所有窗口
```

* 截图

```
super+alt+A #交互式截图
super+ctrl+A #对窗口进行截图
super+ctrl+shitf+a #截图（全屏截图）
```

 * 无障碍

```
屏幕阅读 禁用
alt+super+0 #开关屏幕缩放 
```

* 窗口

```
super+Q #关闭窗口
super+F #切换最大化
super+alt+F #切换全屏
```

* 系统

```
ctrl+super+S #打开快速设置菜单
super+G #显示全部应用
```

* 自定义快捷键<快捷键>   <命令>

```
super+B   firefox
super+T   ghostty
super+`    missioncenter
super+E   nautilus
super+shift+S   flatpak run be.alexandervanhee.gradia --screenshot=INTERACTIVE
super+M gnome-text-editor
ctrl+alt+S gnome-control-center
```

## 功能性扩展

#### ⚠️ 警告：扩展在gnome桌面环境大版本更新的时候大概率会大面积失效，如果出现gnome桌面环境的大版本更新，一定要先关闭所有扩展，谨慎行事

（9月23日gnome更新了49版本，部分扩展尚未更新）

- 从商店安装蓝色的扩展管理器

```
flatpak install flathub com.mattjakeman.ExtensionManager
```

或者可以安装浏览器集成扩展[firefox-gnome-shell-extension-integration](https://addons.mozilla.org/en-US/firefox/addon/gnome-shell-integration/)，然后安装```chrome-gnome-shell```

```
yay -S chrome-gnome-shell
```

这样就可以直接在https://extensions.gnome.org/这个网站通过开关安装扩展了。

- AppIndicator and KStatusNotifierItem Support 

  面板上显示后台应用

- caffeine

  防止熄屏

- lock keys 

  装kazimieras.vaina的那个。osd显示大写锁定和小键盘锁定。设置里把指示器风格改成show/hide cap-locks only

- Fuzzy Application Search

  模糊搜索

- steal my focus window 

  如果打开窗口时窗口已经被打开则置顶

- tiling shell 

  窗口平铺，tilingshell是用布局平铺，记得自定义快捷键，我快捷键是super+w/a/s/d对应上下左右移动窗口，Super+Alt+w/a/s/d对应上下左右扩展窗口，super+Z取消平铺，super+C把窗口移动到屏幕中心

- tiling assistant

  这个扩展提供最基础的四角平铺和上下左右半屏平铺功能。设置里取消激活popup，gaps和tiling shell调成一样的，禁用keybinds里general一项的第1/2/4项，仅保留resote window size。

- 可选：forge

  如果你更喜欢sway/hyprland那样的自动平铺功能，可以安装forge。我没有深入用过这个扩展，说不定会和tiling shell和tilling assitant冲突，所以自己探索吧。

- color picker 

  获取屏幕上的颜色，对自定义非常有用

- Arch Linux Updates Indicator

  在面板上显示一个和arch更新相关的图标。要安装pacman-contrib。设置取消始终显示，高级设置里命令改成

  ```
  ghostty -e sudo pacman -Syu
  ```

- quick settings tweaks

  让右上角的快速设置面板变得更合理。包括把通知从时间面板移动到快速设置面板，缩小时间面板的占地面积，免打扰模式开关按钮移动到快速设置面板，允许调整单个应用的声音大小等等。

  扩展设置的menu页面的两项可以激活，第一项让声音调整菜单以悬浮的方式显示出来，第二项给这个功能增加动画，很酷。

- clipboard indicator 

  剪贴板历史。设置里设置super+v切换菜单

可选：使用鼠标的用户建议安装的扩展

- quick close in overview

  在概览里面不用点窗口右上角的叉关闭窗口了，而是使用鼠标中键

- Top Panel Workspace Scroll

  在顶部面板上滚动滚轮切换工作区

- dask to dock 

  把概览里的快捷栏放到桌面上（如果要用windows布局的话不要装这个）。

其他有用扩展见[其他有用的扩展](#其他有用的扩展)和[实现windows布局](#实现windows布局)

#### 其他有用的扩展

- desktop widgets （desktop clock）

  在桌面上显示一个时钟组件

- lock screen background 

  更换锁屏背景（默认锁屏是模糊，会透出桌面壁纸，已经很好看了，所以这个意义不大）

- vitals 

  右上角显示当前资源使用情况

- emoji copy 

  快捷复制emoji，很有趣

- burn my windows 

  应用开启和打开的动画

- hide activities button 

  隐藏左上角的activities按钮

- Quick Settings Audio Panel

  让你快捷地在右上角的面板里调整每个软件、网页的音频。quick settings tweaks扩展包含了这个功能，如果安装了就不要装这个啦。

- battery time

  显示电量剩余可用时间

- custom reboot

  可以快捷重启到别的系统。设置里选择使用grub，然后在快捷设置菜单里reload和enbale。（grub放在btrfs的话没法使用这个）

- search light

  可以在桌面直接调出搜索，不用进overview才能搜索了。

## 美化用扩展

- blur my shell 

  透明度美化

- hide top bar 

  隐藏面板（panel，顶部的那个面板，win叫任务栏），和app icons taskbar/ dash to panel之类的扩展冲突。设置里激活sensitivity的第一个。intellihide取消激活第二项。

- user themes 

  管理主题

- logo menu 

  在面板显示一个logo，好玩。设置里更换gnome extension为extension manager；终端换ghostty；systemmonitor换missioncenter；取消激活show activities button

- desktop cube 

  桌面端用户强烈推荐。把工作区切换从平铺变成一个可以旋转的方块的面。设置的overview里把透明度（opacity）都改成50%，超级酷！

- rounded window corners reborn 

  让所有窗口变成圆角。这个扩展是真神。 设置里取消激活skip libadwaita applications，然后把corner radius改成14，这样就和gnome的圆角没区别了。

## 实现windows布局

可以通过扩展把gnome变成windows布局

1. 安装扩展

   - app icons taskbar

     实现windows那样的任务栏。和hide top bar、dash to dock冲突。如果关闭了在所有显示器上显示就无法智能隐藏，原因不明。

   - desktop icons ng

     实现windows那样的桌面快捷方式。如果快捷方式打了个叉让你设置执行权限的话在终端去到~/Desktop目录，然后运行这个命令设置相信元数据。这是gnome的一个安全措施。

     ```
     gio set ~/Desktop/*.desktop "metadata::trusted" true
     ```

   - 可选：arcmenu

     这是功能强大的开始菜单扩展。需要pacman安装gnome-menus。

   - 可选：just perfection

     功能强大的自定义扩展，可以设置gnome各个元素的开关。不过根据gnome版本的不同能设置的选项会有所不同，稳定性堪忧。

   - 可选：用dash to panel 替换app icons taskbar

     dash to panel设置更简单，但没有app icons taskbar好看

2. 修改扩展的设置

   - app icons taskbar

     settings页面里：

     激活hide dash in overview（隐藏概览里的快捷栏）

     app icons position in panel （软件图标在面板上的位置）改成center

     激活show all apps button（显示所有软件按钮），位置选right（右边）

     icon size（图标大小）和padding（间距） 按需调整

     panel里激活intllihide（智能隐藏），设置里把only focused window （仅选中的窗口）改成all windows（所有窗口）

     panel location（面板位置）选bottom（底部）

     panel height（面板高度）调整到合适的数值。

     取消激活show activities button（显示活动按钮）

     show weather near clock（在时钟旁显示天气）选right（右边）

     clock posisiton in panel（时钟在面板中的位置）改成right

     这样布局就和win11一模一样了。

   - desktop icons ng

     取消激活显示个人文件夹、回收站图标

## 调节外接屏幕亮度

[ddcutil-service](https://github.com/digitaltrails/ddcutil-service)

gnome默认没法调节外接屏幕亮度，通过ddcutil+扩展可以进行调节。

```
yay -S ddcutil-service
```

```
sudo gpasswd -a $USER i2c
```

安装扩展[Control monitor brightness and volume with ddcutil](https://extensions.gnome.org/extension/6325/control-monitor-brightness-and-volume-with-ddcutil/)

```
reboot
```

### ddcutil使用方法

获取显示器信息

```
ddcutil detect
```

示例输出：

```shell
Display 1
   I2C bus:  /dev/i2c-14
   DRM_connector:           card1-DP-2
   EDID synopsis:
      Mfg id:               SKG - UNK
      Model:                H27T22C
      Product code:         10099  (0x2773)
      Serial number:
      Binary serial number: 1 (0x00000001)
      Manufacture year:     2024,  Week: 46
   VCP version:         2.1
```

注意第一行的Display 1

获取当前亮度

```shell
ddcutil --display 1 getvcp 10
```

加减亮度

```shell
ddcutil --display 1 setvcp 10 + 5
ddcutil --display 1 setvcp 10 -- 5
```

输出应该为

```
‘ghostty’
’-e‘
```

## 光标主题

主题下载网站 https://www.gnome-look.org/browse?cat=107&ord=latest

将下载的.tar.gz文件里面的文件夹放到`～/.local/share/icons/`目录下，没有icons文件夹的话自己创建一个

## gnome主题

gnome的默认主题已经相当漂亮，如果有修改主题的需要的话去这个网站：

https://www.gnome-look.org/browse?cat=134&ord=latest

通常下载页面都有指引，文件路径是`~/.themes/`，放进去之后在user themes扩展的设置里面改可以改主题。

主题主要分gtk-3、gtk-4和gnome-shell。user themes扩展和gnome-tweaks之类的软件修改gnome-shell比较方便。修改gkt-3主题的话建议用nwg-look，gtk-4的文件自己复制粘贴到`~/.config/gtk-4.0/`

## 下一节：[终端美化](终端美化)


## 导出gnome快捷键

用`dconf dump 路径` 命令导出dconf配置到文件。用`dconf load 路径`命令导入。快捷键的配置可以使用`dconf-editor`查找
