# Linux Mint Fcitx5 Chinese Input Method Installation Guide | Linux Mint Fcitx5 中文输入法安装指南

A solution for configuring the Fcitx5 Chinese input method on Linux Mint and resolving GUI freezing caused by conflicting or misconfigured Fcitx/IBus input-method environments.
This guide is based on an issue I encountered on Linux Mint. In my case, removing the existing IBus and Fcitx configuration and switching completely to Fcitx5 resolved the GUI freezing issue.

本教程介绍如何在 Linux Mint 上安装并配置 Fcitx5 中文输入法，以及如何解决由于 Fcitx 和 IBus 输入法同时存在而可能导致的系统图形界面卡死的问题。
这是基于我本人第一次使用 Mint 上遇到的问题。在我的环境中，清除已有的 IBus 和 Fcitx 配置，并完全切换至 Fcitx5 后，问题解决。

---

# English

## Problem

When installing a Chinese input method on Linux Mint, you may encounter problems after changing the default input-method framework. In my case, after configuring Fcitx as the default input method and switching keyboard/input languages, the desktop GUI appears unresponsive.

This was resolved by removing the conflicting IBus and legacy Fcitx packages and configuring the system to use Fcitx5 consistently.

> **Note:** Having both IBus and Fcitx installed does NOT necessarily cause system freezes on every Linux installation. The issue may depend on the Linux Mint version, desktop environment, installed packages, or input-method configuration.

## Possible Cause

Linux applications can use different input-method frameworks, including IBus, Fcitx, and Fcitx5. Problems may occur when multiple frameworks are installed or configured inconsistently. For example, GTK applications may attempt to use one framework while Qt applications or XIM use another.

This can result in an inconsistent input-method stack and may cause symptoms such as:

* Input methods failing to switch
* Chinese input not working in some applications
* Application windows becoming unresponsive
* Keyboard focus problems
* Desktop GUI freezing or severe lag

The Chinese IME itself is not necessarily responsible for the freeze. The issue may from interactions with the input-method framework or the Cinnamon/X11/Wayland session.

The solution used in this guide is to remove the conflicting frameworks and configure the system to use **Fcitx5 consistently and entirely!!**.

---

## Installation

### 1. Remove IBus

```bash
sudo apt remove --purge ibus ibus-*
```

This removes IBus and its related packages to prevent them from interfering with the Fcitx5 configuration.

> **Warning:** Review the package list shown by `apt` before confirming the removal. Depending on your Linux Mint installation, other packages may depend on IBus.

---

### 2. Remove Legacy Fcitx

```bash
sudo apt remove --purge fcitx fcitx-*
```

This removes the legacy Fcitx packages and avoids mixing Fcitx4 components with Fcitx5.

Again, review the packages that `apt` intends to remove before confirming.

---

### 3. Install Fcitx5

Update the package list:

```bash
sudo apt update
```

Install Fcitx5, the Chinese input-method addons, and the Qt configuration tool:

```bash
sudo apt install fcitx5 fcitx5-chinese-addons fcitx5-config-qt
```

This installs the Fcitx5 framework and Chinese input methods such as Pinyin.

---

### 4. Set Fcitx5 as the Default Input Method

Run:

```bash
im-config -n fcitx5
```

This configures Fcitx5 as the system's selected input-method framework.

You can check the current input-method configuration with:

```bash
im-config -m
```

---

### 5. Configure Environment Variables

Open your profile:

```bash
nano ~/.profile
```

Add the following lines:

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

Save the file.

You can load the variables into the current shell with:

```bash
source ~/.profile
```

However, running `source ~/.profile` only updates the current environment. Applications and desktop components that are already may need to restart to adopt the new varaibles.

Therefore, **log out and log back in**, or reboot the system, to apply all these changes.

---

### 6. Start Fcitx5

For testing, Fcitx5 can be started manually with:

```bash
fcitx5 -d
```

The `-d` option starts Fcitx5 as a daemon.

After Fcitx5 has been configured correctly, it should normally start automatically. You should not need to run this command manually after every reboot.

---

### 7. Verify That Fcitx5 Is Running

Run:

```bash
pgrep -a fcitx
```

You should see an Fcitx5 process in the output.

You can also run:

```bash
fcitx5-diagnose
```

`fcitx5-diagnose` provides detailed information about your Fcitx5 installation, environment variables, and other environmental settings. It is especially useful if the input method still does not work correctly.

---

### 8. Add Chinese Pinyin

Open the Fcitx5 configuration tool:

```bash
fcitx5-configtool
```

In the configuration window:

1. Find **Pinyin** in the list of available input methods.
2. Add it to the list of active input methods.
3. Apply/save the configuration if necessary.

You should now have both your normal keyboard layout and Chinese Pinyin available through Fcitx5.

---

### 9. Test the Chinese Input Method

Open a text editor or another application and press:

```text
Ctrl + Space
```

This is the default keybind for switching between input methods in many Fcitx5 configurations. If the Fcitx5 notification/input panel appears and Pinyin input works correctly, the configuration is complete.

Changing input-method switching keybind via:

```bash
fcitx5-configtool
```

---

## Troubleshooting

### Check the Current Input-Method Framework

```bash
im-config -m
```

Make sure the configuration points to Fcitx5 rather than a mixture of different frameworks.

### Check Environment Variables

```bash
env | grep -E 'GTK_IM_MODULE|QT_IM_MODULE|XMODIFIERS'
```

For this configuration, the values should consistently point to Fcitx.

### Check Running Input-Method Processes

```bash
ps aux | grep -E 'fcitx|ibus'
```

If both Fcitx5 and IBus daemons are running, check your startup applications and input-method configuration.

### Run Fcitx5 Diagnostics

```bash
fcitx5-diagnose
```

This is usually the best starting point for diagnosing Fcitx5 configuration problems.

---

# 中文

## 问题描述

在 Linux Mint 上安装中文输入法时，更改默认输入法框架后可能会遇到输入法异常甚至桌面 GUI 卡死的问题。

我将 Fcitx 设置为默认输入法，尝试切换输入语言后，桌面 GUI 偶尔会出现无响应的情况。

最终，通过清除可能产生冲突的 IBus 和旧版 Fcitx，并统一使用 **Fcitx5**，该问题得到了解决。

> **注意：** 同时安装 IBus 和 Fcitx 并不意味着 GUI 卡死一定会发生！！实际情况可能与 Linux Mint 版本、桌面环境、X11/Wayland Session、已安装的软件包以及输入法配置有关。

---

## 可能的原因

Linux 应用程序可以通过不同的输入法框架处理文字输入，例如：

* IBus
* Fcitx
* Fcitx5

当多个输入法框架同时存在，同时enable可能导致输入法栈异常。

例如，GTK 程序尝试使用输入法框架1，而 Qt 程序又尝试使用框架2, 导致：

* 无法正常切换输入法
* 部分程序无法输入中文
* 程序窗口无响应
* 键盘焦点异常
* 桌面 GUI 卡死或严重卡顿

因此，问题不一定来自中文输入法本身，也可能来自输入法框架、GTK/Qt、D-Bus，以及 Cinnamon/X11/Wayland Session 之间的交互。

本教程采用的解决方案是：**清除可能产生冲突的输入法框架，并统一使用 Fcitx5。**

---

## 安装步骤

### 1. 卸载 IBus

```bash
sudo apt remove --purge ibus ibus-*
```

该命令会删除 IBus 及其相关软件包，从而减少其与 Fcitx5 配置产生冲突的可能性。

> **警告：** 在确认卸载之前，请检查 `apt` 显示的待删除软件包列表！！根据你的 Linux Mint 安装环境，部分软件包可能依赖 IBus。

---

### 2. 清除旧版 Fcitx

```bash
sudo apt remove --purge fcitx fcitx-*
```

该步骤用于删除旧版 Fcitx 软件包，避免 Fcitx4 组件与 Fcitx5 混合使用。

同上，请检查 `apt` 将要删除的软件包。

---

### 3. 安装 Fcitx5

首先更新软件包列表：

```bash
sudo apt update
```

然后安装 Fcitx5、中文输入法组件以及 Qt 配置工具：

```bash
sudo apt install fcitx5 fcitx5-chinese-addons fcitx5-config-qt
```

其中 `fcitx5-chinese-addons` 提供包括拼音在内的中文输入支持。

---

### 4. 将 Fcitx5 设置为默认输入法

运行：

```bash
im-config -n fcitx5
```

该命令将 Fcitx5 设置为系统选择的输入法框架。

可以通过以下命令检查当前输入法配置：

```bash
im-config -m
```

---

### 5. 配置环境变量

打开用户 Profile：

```bash
nano ~/.profile
```

加入：

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

保存文件。

可以通过以下命令让当前 Shell 读取新的配置：

```bash
source ~/.profile
```

但是需要注意，`source ~/.profile` **只会更新当前 Shell 的环境变量**。因此修改完成后，建议**重新登录 Linux Mint，或者直接reboot，因为已经运行的桌面组件和应用程序不一定会读取新的环境变量 **。

---

### 6. 启动 Fcitx5

测试时可以手动运行：

```bash
fcitx5 -d
```

其中 `-d` 表示以 daemon（后台服务）的方式运行 Fcitx5。

完成正确配置并重新登录桌面环境后，Fcitx5 即可自动启动，正常情况下不需要每次开机都手动运行该命令。

---

### 7. 检查 Fcitx5 是否正常运行

运行：

```bash
pgrep -a fcitx
```

如果能够看到 Fcitx5 相关进程，则说明 Fcitx5 已经启动。

还可以运行：

```bash
fcitx5-diagnose
```

该命令会检查 Fcitx5 的安装状态、环境变量、GTK/Qt 支持、Addon 以及其他相关配置。

如果 Fcitx5 仍然无法正常工作，建议首先检查 `fcitx5-diagnose` 的输出。

---

### 8. 添加中文拼音输入法

运行：

```bash
fcitx5-configtool
```

打开 Fcitx5 配置窗口后：

1. 在可用输入法中找到 **Pinyin（拼音）**。
2. 将其添加到当前启用的输入法列表。
3. 如有需要，保存或应用配置。

完成后，Fcitx5 中应该同时存在普通键盘布局和中文拼音输入法。

---

### 9. 测试中文输入

打开文本编辑器或其他可以输入文字的软件，然后按：

```text
Ctrl + Space
```

在许多 Fcitx5 配置中，这是默认的输入法切换快捷键。如果能够正常出现 Fcitx5 输入窗口并使用拼音输入中文，则配置成功。

如果需要修改输入法切换快捷键，可以再次打开：

```bash
fcitx5-configtool
```

进行自定义。

---

## 故障排查

### 检查当前默认输入法

```bash
im-config -m
```

确认系统使用的是 Fcitx5，而不是多个输入法框架的混合配置。

### 检查输入法环境变量

```bash
env | grep -E 'GTK_IM_MODULE|QT_IM_MODULE|XMODIFIERS'
```

在本教程的配置下，这些变量应该保持一致并指向 Fcitx。

### 检查正在运行的输入法进程

```bash
ps aux | grep -E 'fcitx|ibus'
```

如果发现 Fcitx5 和 IBus daemon 同时运行，可以进一步检查系统的启动程序和输入法配置。

### Fcitx5 综合诊断

```bash
fcitx5-diagnose
```

如果输入法仍然存在问题，这通常是最值得首先运行的诊断命令。

---

## Final Notes | 最后澄清

This guide documents a solution that worked on my Linux Mint installation. Linux desktop environments and input-method configurations vary, so the exact cause and required configuration may differ between systems.

If you encounter a similar problem, `fcitx5-diagnose` is highly recommended for identifying configuration issues before making additional system changes.

本教程记录的是在我的 Linux Mint 环境中实际有效的解决方案。由于不同 Linux 桌面环境、系统版本和输入法配置可能存在差异，其他设备出现类似问题时，具体原因和解决方法可能有所不同。

如果按照本教程配置后仍存在问题，建议首先运行 `fcitx5-diagnose` 检查系统的输入法环境。
