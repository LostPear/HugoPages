---
title: "rEFInd 安装及简单配置指南"
date: 2024-04-29 14:55:00
lastmod: 2024-12-26 23:57:24
tags:
  - 教程
categories:
  - 教程
---
> 这几天试着往电脑上安装FydeOS，不知道为什么FydeOS自带的rEFInd安装不上去，就试着自己搞了一下，在此整理了自己配置的步骤。

>

> rEFInd是一个UI更为现代化的启动引导器，在Linux/Mac下，rEFInd可以使用自带的脚本安装，Windows则需要自己配置。以下内容适用于使用EFI启动的Windows平台。

>

> **Surface设备以及部分电脑配置rEFInd时，可能需要关闭安全启动（Secure Boot）。**

# 1.安装rEFInd

从[官网](http://sourceforge.net/projects/refind/files/0.14.2/refind-bin-0.14.2.zip/download)下载rEFInd的相关文件，得到一个zip结尾的压缩文件。打开压缩文件，将里面的`refind`文件夹复制出来。

打开DiskGenius，在左侧的窗口里展开`SYSTEM`分区，双击`EFI`文件夹，然后把刚刚解压出来的`refind`文件夹拖到右侧的窗口里，把文件夹复制到分区里。

然后点击上方的`工具`选项卡，点击`设置UEFI BIOS启动项`，在弹出来的窗口中点击`添加`，然后在新窗口中分别打开`EFI`，`refind`文件夹，双击`refind_x64.efi`，软件会提示“设置完成”，列表中会多一个名叫“New Boot Entry”的启动项。把这个启动项改名为“rEFInd”，点击`保存当前启动项设置`，然后再把它移动到第一位。

到这里，rEFInd的安装就安装完毕了。此时重启电脑，应该可以看到rEFInd的启动界面。

# 2.配置rEFInd

rEFInd的默认主题不是很好看，启动项也可能会有一些缺失，因此我们还需要做一些配置来舒适使用。在rEFInd的文件目录下可以找到一个名叫`redind.conf-sample`的文件，这个就是配置文件模板。把这个文件复制一份，重命名为`refind.conf`，然后用文本编辑器打开。这个文件里，我们一般需要配置这几项：

## 基础配置

```

# Timeout in seconds for the main menu screen. Setting the timeout to 0

# disables automatic booting (i.e., no timeout). Setting it to -1 causes

# an immediate boot to the default OS *UNLESS* a keypress is in the buffer

# when rEFInd launches, in which case that keypress is interpreted as a

# shortcut key. If no matching shortcut is found, rEFInd displays its

# menu with no timeout.

#

timeout 20

```

这一项配置等待时间，默认是20秒，建议改为5秒。

```

# Set the screen's video resolution. Pass this option one of the following:

#  * two integer values, corresponding to the X and Y resolutions

#  * one integer value, corresponding to a GOP (UEFI) video mode

#  * the string "max", which sets the maximum available resolution

# Note that not all resolutions are supported. On UEFI systems, passing

# an incorrect value results in a message being shown on the screen to

# that effect, along with a list of supported modes. On EFI 1.x systems

# (e.g., Macintoshes), setting an incorrect mode silently fails. On both

# types of systems, setting an incorrect resolution results in the default

# resolution being used. A resolution of 1024x768 usually works, but higher

# values often don't.

# Default is "0 0" (use the system default resolution, usually 800x600).

#

#resolution 1024 768

#resolution 1440 900

#resolution 3

#resolution max

```

这一项设置分辨率，一般的格式为`resolution 横边像素 竖边像素`。建议直接设置为`max`，显示为支持的最大分辨率。配置时需要删掉`resolution`前的井号。

```

# Enable touch screen support. If active, this feature enables use of

# touch screen controls (as on tablets). Note, however, that not all

# tablets' EFIs provide the necessary underlying support, so this

# feature may not work for you. If it does work, you should be able

# to launch an OS or tool by touching it. In a submenu, touching

# anywhere launches the currently-selection item; there is, at present,

# no way to select a specific submenu item. This feature is mutually

# exclusive with the enable_mouse feature. If both are uncommented,

# the one read most recently takes precedence.

#

#enable_touch

# Enable mouse support. If active, this feature enables use of the

# computer's mouse. Note, however, that not all computers' EFIs

# provide the necessary underlying support, so this feature may not

# work for you. If it does work, you should be able to launch an

# OS or tool by clicking it with the mouse pointer. This feature

# is mutually exclusive with the enable_touch feature. If both

# are uncommented, the one read most recently takes precedence.

#

#enable_mouse

```

这两项分别配置触控支持和鼠标支持，建议开启，删掉末尾行前的井号即可。

## 启动项配置

一般来说，rEFInd会自动搜索和添加启动项，如果没有对应的启动项，则需要我们自行配置。常用的配置参数一般如下：

```

menuentry "启动项名称" {

    loader 启动文件目录

    icon 图标目录

    }

```

举个例子，如果要配置Windows启动项，那么配置参数应该写成这样：

```

menuentry "Windows" {

    icon /EFI/refind/icons/os_win.png

    loader /EFI/Microsoft/Boot/bootmgfw.efi

    }

```

## 主题配置

rEFInd的默认界面确实有点不好看，不过我们可以自己安装主题来美化。[这个页面](https://github.com/topics/refind-theme)下整理了很多rEFInd的主题，要使用的话直接下载整个仓库的文件就可以。在refind的目录下新建一个名叫`themes`的文件夹，然后把主题文件夹复制进去，再在配置文件末尾添加这样一行：

```

include themes/主题文件夹的名称/theme.conf

```

这样就配置好了。

## 隐藏多余的启动项

> **这一步需要在默认主题下进行。**

有些启动项可能会重复，在启动界面按delete键即可隐藏对应的启动项。

到这里rEFInd的安装和基本配置就完毕了。rEFInd的玩法远远不止如此，想要了解更多的话，可以参阅官方文档。
