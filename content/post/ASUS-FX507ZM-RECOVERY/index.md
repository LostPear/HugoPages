---
title: "天选 3 重建恢复分区教程"
date: 2025-06-05 10:29:00
lastmod: 2025-06-09 20:35:33
categories:
  - 教程
---
> 这篇文章需要的资源可以从[这里](https://alist.shojola.top/%E5%AE%89%E8%A3%85%E6%98%A0%E5%83%8F/Windows/ASUS%20FX507ZM)找到。

这篇文章适合 ASUS FX507ZM 的机型，不保证其他机型可用。其他机型请自行寻找对应的资源。

> 如果你只是单纯想用原厂系统，下载文章内的压缩包后，打开`RESTORE`文件夹，将里面的 swm 文件直接恢复到系统盘中，然后重建引导即可。

## 准备恢复文件

打开 DiskGenius，删除系统盘最后面原来的恢复分区，在系统盘后面按顺序建立以下三个分区：

 1. 大小 >= 800 MB，修改卷标为 RECOVERY；

 2. 大小 >= 20 GB，修改卷标为 RESTORE；

 3. 大小 >= 200 MB，修改卷标为 MYASUS；

建立完成后，把压缩包内对应文件夹下的全部文件，复制到对应名字的分区里。

然后在 DiskGenius 内，把 ESP 分区的卷标改为 SYSTEM。

## 重建恢复分区

打开带管理员权限的命令提示符，先输入以下命令：

```powershell

reagentc /info

```

此时应该会看到 Windows RE 的状态为 `Disabled`。

记下刚刚建立的卷标为 RECOVERY 的分区盘符（这里假定为 E:\），输入以下命令：

```powershell

reagentc /setreimage /path "E:\Recovery\WindowsRE"

```

提示成功后，紧接着输入以下命令：

```powershell

reagentc /setbootshelllink /configfile "E:\Recovery\WindowsRE\ReCustomization.xml"

```

提示成功后进行下一步。

## 标识恢复分区

在 DiskGenius 内，删除三个分区的盘符，然后修改分区参数，做以下修改并保存：

RECOVERY 和 MYASUS 分区：文件系统类型更改为`Microsoft recovery partition`，勾选`无盘符`和`OEM`；

RESTORE 分区：勾选`无盘符`和`OEM`。

## 启用恢复分区

在带管理员的命令提示符中输入以下命令：

```powershell

reagentc /enable

```

如果一切顺利，会收到启用成功的消息。此时再运行`reagentc /info`，应该也可以看到启用的状态。

打开开始菜单，按住键盘的`Shift`点击`重启`，会进入 Windows RE 的界面。点击`疑难解答`，应该可以看到`MyASUS in WinRE`的选项，同时各工具工作正常。

至此恢复步骤结束。可以正常使用。
