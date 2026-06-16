

1. 网上下载grub主题放到`/efi/grub/themes`  

    其他可能会用到的路径有`/usr/share/grub/themes/`和`/boot/grub/themes`。

2. 编辑grub源文件

    ```
    vim /etc/default/grub
    ```
    - 主题路径
        `GRUB_THEME="/path/to/theme.txt"`

    - 分辨率
        `GRUB_GFXMODE=2560X1440,1920x1080,auto`

3. 生成grub的配置文件

    ```
    grub-mkconfig -o /efi/grub/grub.cfg
    ```

我喜欢的主题：

[CyberGRUB-2077](https://github.com/adnksharp/CyberGRUB-2077)

[Crossgrub](https://github.com/krypciak/crossgrub)

这个repo有一些别人收集的主题：

https://github.com/Jacksaur/Gorgeous-GRUB

## 下一节：[显卡切换](显卡切换)