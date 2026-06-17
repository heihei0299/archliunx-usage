下面用**清晰、结构化、可复现问题定位思路**帮你总结：

---

# 🧩 一、用户遇到的问题是什么？

用户在 Arch Linux + Niri 桌面环境下，使用以下工具时出现字符显示异常：

* VSCode 内置终端
* opencode（TUI 终端程序）
* zsh + starship 提示符
* kitty 终端（基本正常）

## ❗ 具体表现

界面中出现大量：

* 黑块（■）
* 空白方块
* 字符断裂
* 中文路径显示异常
* Git/终端图标不显示（变方块）

例如：

* Nerd Font 图标（  ）显示为方块
* 中文目录名称显示为 ■ 或空白
* 部分 ASCII 字符也异常断裂

---

# 🔍 二、问题的本质原因是什么？

这个问题本质不是单一错误，而是**字体系统不完整 + fallback 链错误**导致的。

可以拆成 3 个核心原因：

---

## 1️⃣ Nerd Font 字符缺失（图标变黑块）

starship / opencode / git prompt 使用：

* Powerline symbols
* Nerd Font private use area glyph
* box drawing symbols

👉 当前系统中使用的 Nerd Font：

* 版本不完整（非 complete patched）
* 或 VSCode 没正确加载 Mono 版本

结果：

> 图标字符找不到 → 显示黑块

---

## 2️⃣ 中文字体缺失（CJK fallback 不工作）

路径中包含中文，例如：

```
配置 / learning-project
```

但 terminal 没有正确 fallback 到：

* Noto Sans CJK

结果：

> 中文 glyph 缺失 → 显示 ■ 或空白

---

## 3️⃣ VSCode / opencode 字体 fallback 链错误（最关键）

VSCode terminal 与 kitty 不同：

* kitty：支持 font fallback（自动补字体）
* VSCode terminal：**只依赖手动配置的字体列表**

当前问题是：

> VSCode terminal 没有正确配置多字体 fallback 顺序

导致：

* 英文 OK
* Nerd font ❌
* 中文 ❌
* emoji ❌

最终结果：大量黑块

---

# ⚠️ 三、为什么 kitty 正常但 VSCode / opencode 不正常？

因为：

| 终端              | 字体机制        | 结果 |
| --------------- | ----------- | -- |
| kitty           | 自动 fallback | 正常 |
| VSCode terminal | 手动字体链       | 失败 |
| opencode (TUI)  | 依赖终端字体      | 失败 |

👉 kitty 掩盖了问题的严重性

---

# 🛠 四、正确解决办法

必须同时解决三层字体：

---

## ✅ 1. 安装完整字体集合（系统层）

```bash
sudo pacman -S \
ttf-jetbrains-mono-nerd \
noto-fonts \
noto-fonts-cjk \
noto-fonts-emoji \
ttf-nerd-fonts-symbols-mono
```

---

## ✅ 2. VSCode terminal 字体配置（关键）

```json
"terminal.integrated.fontFamily":
"JetBrainsMono Nerd Font Mono, Symbols Nerd Font Mono, Noto Sans CJK SC, Noto Color Emoji"
```

---

## ✅ 3. 强制 Mono Nerd Font（避免 glyph 缺失）

确保使用：

* JetBrainsMono Nerd Font Mono
  而不是：
* JetBrains Mono
* Nerd Font（非 Mono）

---

## ✅ 4. 重启 VSCode

必须执行：

```
Ctrl + Shift + P → Reload Window
```

---

# 🎯 五、最终效果（修复后）

修复成功后应达到：

* ✔ starship 图标正常
* ✔ git branch 正常显示
* ✔ opencode UI 无黑块
* ✔ 中文路径正常
* ✔ emoji 正常显示
* ✔ VSCode + kitty 一致

---

# 🧠 一句话总结

> 问题的本质是：终端字体 fallback 链断裂 + Nerd Font / CJK / Emoji 字体未统一配置，导致 VSCode 与 TUI 程序无法正确渲染字符，从而出现大量黑块。

---

如果你需要，我可以帮你做一份**“Arch 全局字体统一配置方案（VSCode + kitty + terminal + TUI 100%一致）”**，可以彻底避免以后再出现这种问题。
