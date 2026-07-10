---
title: "中兴 B860AV1.1 免拆机 SuperSU 接管 root 教程"
date: 2023-08-02 17:50:00
lastmod: 2025-02-07 15:14:52
tags:
  - 教程
categories:
  - 教程
slug: "Get-Root-On-ZTE-TVBox"
---
> 家里有一个办宽带送的电视盒子，但是1+8，安卓4.4的配置已经是太太太老了，还预装了一堆IPTV的东西拖累运行速度，况且安卓4.4也不支持adb激活冰箱，于是就只能通过root来解决这个问题了。不过网上的教程都需要通过拆机接TTL线来搞，实在是有点麻烦，考虑到拆机核心思路也就是拿到shell的root权限，如果我们能在系统中直接拿到root权限就好办了。安卓4.4时代正是各种一键root工具开花的时候，因此上面这种思路就变得好实现了。<br>
**免责声明：本文只提供搞机的思路，由此带来的损失本人不承担任何责任！**<br>
需要的工具你可以在[这里][1]进行下载。<br>

# 开启adb调试

下载`STB_ConfigTool_V7.6.7_Bin_Site.rar`，解压其中的bin文件夹（密码：qizhi），双击StbCfgTool.exe。打开后先点击`Character Info`，右边会出来一段字符，复制下来，然后点击`Next`，把刚刚得到的这串字符复制进去，点`下一步`，就可以进到主页面了。

> Windows 10以上的系统会出现界面显示不全的现象，不需要去理会。<br>

将你的机顶盒连接到无线网络，注意必须和电脑在同一个局域网内（也就是连接到一个路由器的网上）。在配置工具内在`ipv4`一栏输入你机顶盒的IP地址，点击`连接`，等待连接成功后再点`打开 ADB`。如果在等待一段时间后，按钮变为`关闭 ADB`，那么这一步的设置就完成了。

下载[adb工具包](https://developer.android.google.cn/studio/releases/platform-tools?hl=zh-cn)并解压，在工具包的目录下打开cmd。为了方便描述，在这里假定盒子的IP地址为`192.168.0.5`。

打开cmd后，输入以下命令远程连接盒子：

```shell
adb connect 192.168.0.5
```

如果出现`connected to 192.168.0.5:5555`就代表这一步完成了。为了避免不必要的麻烦，我们先看看能不能直接获取到adb shell的root权限。往里面输入

```shell
adb shell
```

此时前面应该会变成`shell@orange:/ $`这样的字样。输入`su`，观察`$`是否会变成`#`，同时前面应该会变成`root@orange:/ #`这样的字。如果可以的话，那么直接就可以卸载掉不必要的软件了，如果报错的话，那么我们就要手动进行root权限的接管。

# 获取root权限

下载`360cjroot-7.0.7.apk`，回到刚才的cmd窗口。先输入`exit`退出shell模式，然后我们用adb把apk安装到盒子上。为了方便描述，在这里我们假定安装包路径为`D:\360root.apk`。

向cmd里输入

```shell
adb install D:\360root.apk
```

等待安装完成。安装完成后，转到电视盒子插上鼠标，打开360root，点击`一键ROOT`，等待root完毕。

> 等待过程中很可能会出现电视盒子卡死，或者提示root失败等，多尝试几次再确认是否可行。<br>

root完毕之后，重复前面的步骤，确认是否可以取得shell的root权限。可行之后转到下一步。

> 如果盒子弹出了给予名字为`Shell`的APP请求root权限的对话框，请选择允许。<br>

# 接管root权限

下载`SuperSU.zip`并解压，回到我们刚刚已经获取了权限的shell中。

先用`mkdir`命令创立一个目录，我们要把这个目录作为临时中转站：

```shell
cd /sdcard/
mkdir SuperSUTemp
```

随后，我们运行两次`exit`命令退出shell模式。就像这样：

```shell
root@orange:/ # exit
shell@orange:/ $ exit
C:\Users\Rinko24>
```

在这里我们假定你把SuperSU的文件复制出来的目录是`D:\SuperSU`。运行以下命令，把相关文件复制到临时目录里：

```shell
adb push D:\SuperSU\su /sdcard/SuperSUTemp/
adb push D:\SuperSU\su /sdcard/SuperSUTemp/
adb push D:\SuperSU\supolicy /sdcard/SuperSUTemp/
adb push D:\SuperSU\supolicy /sdcard/SuperSUTemp/
adb push D:\SuperSU\chattr /sdcard/SuperSUTemp/
adb push D:\SuperSU\chattr /sdcard/SuperSUTemp/
adb push D:\SuperSU\suinit /sdcard/SuperSUTemp/
adb push D:\SuperSU\suinit /sdcard/SuperSUTemp/
adb push D:\SuperSU\sukernel /sdcard/SuperSUTemp/
adb push D:\SuperSU\sukernel /sdcard/SuperSUTemp/
adb push D:\SuperSU\daemonsu /sdcard/SuperSUTemp/
adb push D:\SuperSU\daemonsu /sdcard/SuperSUTemp/
adb push D:\SuperSU\libsupol.so /sdcard/SuperSUTemp/
```

然后进入到root权限的shell中。先把system分区挂载为可读写：

```shell
mount -o remount,rw /system
```

随后，移动临时目录中的文件到system分区下对应的文件夹：

```shell
cp /sdcard/SuperSUTemp/su /system/bin/
cp /sdcard/SuperSUTemp/su /system/xbin/
cp /sdcard/SuperSUTemp/supolicy /system/bin/
cp /sdcard/SuperSUTemp/supolicy /system/xbin/
cp /sdcard/SuperSUTemp/chattr /system/bin/
cp /sdcard/SuperSUTemp/chattr /system/xbin/
cp /sdcard/SuperSUTemp/suinit /system/bin/
cp /sdcard/SuperSUTemp/suinit /system/xbin/
cp /sdcard/SuperSUTemp/sukernel /system/bin/
cp /sdcard/SuperSUTemp/sukernel /system/xbin/
cp /sdcard/SuperSUTemp/daemonsu /system/bin/
cp /sdcard/SuperSUTemp/daemonsu /system/xbin/
cp /sdcard/SuperSUTemp/libsupol.so /system/lib/
```

为这些文件添加权限：

```shell
chmod 4755 /system/bin/su
chmod 4755 /system/xbin/su
chmod 4755 /system/bin/supolicy
chmod 4755 /system/xbin/supolicy
chmod 4755 /system/bin/chattr
chmod 4755 /system/xbin/chattr
chmod 4755 /system/bin/suinit
chmod 4755 /system/xbin/suinit
chmod 4755 /system/bin/sukernel
chmod 4755 /system/xbin/sukernel
chmod 4755 /system/bin/daemonsu
chmod 4755 /system/xbin/daemonsu
chmod 0644 /system/lib/libsupol.so
```

随后设置su文件的自启选项：

```shell
echo "/system/xbin/daemonsu --auto-daemon &" >> /system/bin/init.zte.post_boot.sh
/system/xbin/daemonsu --auto-daemon
```

现在我们已经都设置好了。接下来卸载掉360超级root:

```shell
pm uninstall --user 0 com.qihoo.permmgr
```

然后关闭掉盒子电视自带的安装限制，并设置自动启动：

```shell
setprop config.Android.AppInstallCtrl 3
settings put secure install_non_market_apps 1
settings put global install_non_market_apps 1
echo "setprop config.Android.AppInstallCtrl 3" >>/system/bin/init.zte.post_boot.sh
echo "settings put secure install_non_market_apps 1" >>/system/bin/init.zte.post_boot.sh
echo "settings put global install_non_market_apps 1" >>/system/bin/init.zte.post_boot.sh
```

连续执行两次`exit`退出shell，然后安装SuperSU：

```shell
adb install D:\SuperSU\SuperSU.apk
```

接着转到盒子，把你的盒子网络连接到可以科学上网的环境，打开SuperSU，不出意外会提示你更新二进制文件，点击更新，按照程序的提示进行操作，更新完毕后就可以正常授权了。

接下来的事情就可以自己选择了，卸载预装，安装当贝桌面这些都可以。预装软件建议通过[AirFrozen](https://github.com/hyongbai/AirFrozen)进行冻结，不需要自己去卸载预装，以免不必要的麻烦。

> 参考资料：<br>
[中兴现场配置工具STB_ConfigTool_V7.6.7_Bin_Site](https://www.znds.com/tv-583854-1-1.html)<br>
[大江苏b860av1.1无UBOOT 文件救砖成功新的分享。](https://www.znds.com/tv-980841-1-1.html)<br>
[B860AV1.1-T优化第二弹:获取 root 权限](https://www.znds.com/tv-1115981-1-1.html)<br>

  [1]: https://alist.shojola.top/%E5%8D%9A%E5%AE%A2%E6%96%87%E4%BB%B6/%E6%9C%BA%E9%A1%B6%E7%9B%92%E7%A0%B4%E8%A7%A3%E7%94%A8%E5%B7%A5%E5%85%B7
