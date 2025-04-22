---
title: "Linux字体安装"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["Linux"]
---

# Linux字体安装
在 Linux 系统中安装字体有多种方法，以下是常见的几种方式：

---

### **1. 手动安装字体（适用于用户或系统全局）**
#### **用户级安装（仅当前用户可用）**
- 将字体文件（如 `.ttf`、`.otf` 等）复制到用户字体目录：
  ```bash
  mkdir -p ~/.local/share/fonts
  cp /path/to/your/font.ttf ~/.local/share/fonts/
  ```
- 更新字体缓存：
  ```bash
  fc-cache -fv
  ```

#### **系统级安装（所有用户可用）**
- 需要 root 权限，将字体复制到系统字体目录：
  ```bash
  sudo cp /path/to/your/font.ttf /usr/share/fonts/
  ```
- 更新字体缓存：
  ```bash
  sudo fc-cache -fv
  ```

---

### **2. 使用包管理器安装（推荐）**
- **Debian/Ubuntu**（APT）：
  ```bash
  sudo apt install fonts-<name>  # 例如：fonts-noto, fonts-roboto
  ```
- **Fedora/RHEL**（DNF）：
  ```bash
  sudo dnf install <name>-fonts    # 例如：google-roboto-fonts
  ```
- **Arch Linux**（Pacman）：
  ```bash
  sudo pacman -S <name>-fonts      # 例如：ttf-roboto
  ```

---

### **3. 通过字体管理器工具**
- **GNOME 用户**：使用 `gnome-font-viewer`（双击字体文件后点击 "Install"）。
- **KDE 用户**：使用 `kfontview` 或系统设置中的字体管理模块。

---

### **4. 创建字体子目录（组织多字体）**
- 在 `/usr/share/fonts/` 或 `~/.local/share/fonts/` 下创建子目录（如 `custom`），放入字体后更新缓存：
  ```bash
  sudo mkdir /usr/share/fonts/custom
  sudo cp *.ttf /usr/share/fonts/custom/
  sudo fc-cache -fv
  ```

---

### **5. 临时测试字体**
- 直接运行应用程序时指定字体路径（不永久安装）：
  ```bash
  LIBGL_FREETYPE_FONT_PATH=/path/to/fonts/ your_app
  ```

---

### **注意事项**
1. **字体格式支持**：Linux 常用 `TrueType (.ttf)`、`OpenType (.otf)` 和 `WOFF/WOFF2`（部分支持）。
2. **权限问题**：系统级安装需 `sudo`，用户级安装无需。
3. **查找已安装字体**：使用 `fc-list` 查看字体列表。

---

### **验证安装**
```bash
fc-match <fontname>  # 检查字体是否生效
```
或通过图形软件（如 LibreOffice）查看字体列表。

根据你的需求选择合适的方法，用户级安装更安全，系统级安装需谨慎操作。