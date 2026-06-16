
## 安装

- 切换到root用户

    ```
    suod -i
    ```

- 分区/格式化/挂载和arch是一样的

- 生成基础配置

  ```
  nixos-generate-config --root /mnt
  ```

  会在`/mnt/etc/nixos/`下生成`configuration.nix`和`hardware-configuration.nix`

- 创建flake

  ```
  cd /mnt/etc/nixos
  touch flake.nix home.nix
  ```

  





## references

https://nixos-cn.org/