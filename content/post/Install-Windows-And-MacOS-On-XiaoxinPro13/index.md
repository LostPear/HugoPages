---
title: "联想小新 Pro 13 2020 款 Windows 系统+黑苹果安装记录"
date: 2024-07-27 19:09:00
lastmod: 2026-07-05 11:51:31
categories:
  - 教程
slug: "Install-Windows-And-MacOS-On-XiaoxinPro13"
---
好像目前没怎么看到这个机子装黑果的教程，于是就打算写一篇作为参考了

## 安装 Windows 系统

我这台机子不知道为什么，用原版镜像重装Win11 后，Fn+Q 的热键驱动打不上去，没法开启野兽模式，所以改用联想的 OEM 镜像。下面的创建教程应该适合所有的联想电脑。

想要下载 OEM 镜像需要先创建恢复介质的订单。国内的联想站不提供这个机子的镜像，需要到香港的站点申请。打开[申请的网站]([https://](https://pcsupport.lenovo.com/hk/zh/lenovorecovery))，输入机器编号，点击“送出”。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-1.png)

在下一个页面中，选择恢复镜像的语言并同意条款，点击“下一步”，按照指引登录账号进行操作。登录账号最好使用邮箱进行登录，方便后续接受恢复订单的信息。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-2.png)

中间可能会要求更改网站语言，更改语言后重新按照上面的步骤来就可以了。登录账号后会要求输入个人信息，填写后点击“Submit”。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-3.png)

看到像是下图这样的页面，订单就创建好了。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-4.png)

此时检查你注册账号使用的邮箱，会发现多出来一个订单信息的邮件，按照说明点击蓝色的“RDDS”下载创建工具。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-5.png)

下载创建工具后，按照创建工具里面的指引，创建恢复盘。提前准备好一个32G以上的U盘插入电脑，里面的内容在做启动盘时都会被删除。工具使用前必须要登录联想账号，一定要登录刚刚创建订单使用的联想账号，工具只有在识别到账号下的订单后才可以继续使用。

创建完成后，确保U盘插在电脑上，设置从U盘启动，启动恢复程序。中间会重启几次，耐心等待一会。Pro 13的出厂镜像是Windows 10 2004，恢复好后可以用原版镜像升级到Windows 11。

## 安装黑苹果

我这里安装的是Sonoma 14.2.1，更低版本的系统教程通用。

先简单说一下安装黑苹果的原理，黑苹果安装的关键在于引导文件（也就是EFI），有了对应的EFI之后才可以正常引导系统进行安装。对于小新Pro 13 2020，可以使用[这个项目](https://github.com/daliansky/XiaoXinPro-13-hackintosh)下的引导文件，具体的操作步骤之后会讲。

小新Pro 13安装黑苹果前需要刷破解BIOS并且进行相关设置，详情请参考[这篇教程](https://github.com/daliansky/XiaoXinPro-13-hackintosh/wiki/DVMT%E8%A1%A5%E4%B8%81#%E6%B5%8B%E8%AF%95%E7%89%88bios)。

除了上面教程提到的设置之外，在BIOS中还需要另外修改以下三项（此处参考[这篇教程](https://github.com/daliansky/XiaoXinPro-13-hackintosh/wiki/EFI-OC-PRO13%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)）：

Security选项卡下：

Intel Platform Trust Technology = Disable

Intel SGX Control = Disable 【建议】

Secure Boot = Disable

### 1、准备安装U盘

下载好黑苹果的系统镜像，版本自选，黑苹果镜像分为两种，以dmg结尾的可引导镜像和以iso结尾的不可引导镜像，确保下载的是以dmg结尾的这种。

准备好一个32G以上大小的U盘，用balenaEtcher把镜像写入U盘。写入的时候同样会清除U盘里面的数据，记得提前备份。

如果使用balenaEtcher时，多次尝试仍然写入失败，可以改用TransMac制作启动盘。使用TransMac制作启动盘的方法可以参考[这篇教程](https://www.mfpud.com/topics/940/)。

打开[这个仓库](https://github.com/daliansky/XiaoXinPro-13-hackintosh)，点击绿色的`Code`标签，点击`Download ZIP`把整个仓库的文件下载下来。解压出来EFI文件夹下的EFI_OC文件夹，这个就是我们一会要用到的引导文件。

打开DiskGenius，点击左侧U盘下的EFI分区，删掉EFI目录下的所有文件，然后把刚刚下好的EFI文件替换进去。

![图片来自CSDN：https://blog.csdn.net/iCanCode/article/details/108064539](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-6.png)

### 2、开始安装

用Windows自带的磁盘管理新分出来一个分区，创建好后格式化为NTFS分区。

U盘插入电脑，重启电脑设置从U盘启动，使用左右方向键选择“Install macOS Sonoma”，回车启动。

在安装页面中，先选择“磁盘工具”，点击右上角的“分区”按钮。可以看到两个分区。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-7.png)

选择刚刚新创建的分区，格式选择 APFS，点击“应用”。

退出磁盘工具，点击“安装 macOS”，选择刚刚的分区，按照指引安装操作。

安装过程中会重启，第一次重启过后，需要再从 U 盘引导 macOS，可以看到 OC 的引导界面多了一个启动项，这个就是安装在本地的 macOS。选择这个启动项启动，后面安装时还会重启几次，每次重启都需要从 U 盘引导系统，选择本地 macOS 的启动项，直到看到设置向导为止。此时你会发现还没有网络，不要着急，先不联网把设置向导走完，驱动下一步再补。

### 3、补全驱动，设置本地启动项

现在我们是通过U 盘来启动 macOS，这一步我们需要把黑苹果的引导安装到本地。由于驱动包含在 EFI 内，所以驱动补全也可以同时完成。EFI可以参考前述项目内的配置，配置文件下载可以点击[此处](https://alist.shojola.top/d/%E5%8D%9A%E5%AE%A2%E6%96%87%E4%BB%B6/%E8%81%94%E6%83%B3%E5%B0%8F%E6%96%B0Pro13%202020%E9%BB%91%E8%8B%B9%E6%9E%9C%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6/config.plist?sign=WzIeBkE_9GcryyQEFU4cDsw1SWJrhO8QgXD263d1lpA=:0)。

打开DiskGenius，左侧点击SYSTEM_DRV分区，打开EFI文件夹，把压缩包内的OC文件夹复制进去，然后点开OC文件夹，把我改好的config.plist文件覆盖进去。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-8.png)

然后点击上方的“工具”——“设置UEFI BIOS启动项”，点击左侧的“添加”，文件选择OC文件夹下的OpenCore.efi，然后右侧启动项名称改为“OpenCore”，点击“保存当前启动项设置”，点击左侧的“上移”，把OpenCore放在第一位。点击“取消”退出。

![](https://images.shojola.top/Articles/Install-Windows-And-MacOS-On-XiaoxinPro13/Pro13Tutorial-9.png)

重启电脑，此时就可以进入OpenCore的引导界面了，选择macOS启动，也可以看到网卡已经能用了。这个配置文件已经注入了三码，iCloud也可以正常使用。

到这里所有的工作就都完成了，可以开始体验黑苹果啦~
