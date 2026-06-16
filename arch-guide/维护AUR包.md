

建立并推送

```
git clone ssh://aur@aur.archlinux.org/shorin-dms-niri-meta.git
cd shorin-dms-niri-meta
```

```
makepkg --printsrcinfo > .SRCINFO
```

```
git add PKGBUILD .SRCINFO
git commit -m "Initial commit: Add meta package"
git push 
```

