---
title: "Lineage OS for Redmi K50 编译指南"
date: 2022-08-30 00:00:00
lastmod: 2025-02-07 15:00:34
categories:
  - 教程
---
> 这篇文章中的部分内容可能已经过时。<br>

## 编译前的准备

1.你的电脑最好有一颗强劲的CPU

> 性能差一点也没关系，只是时间会长一点，这方面没有严格的要求。<br>

2.你的电脑需要16GB以上的运行内存

> 如果你的电脑且运行内存为16GB，请最好通过未安装桌面环境的系统操作来提高编译成功率。<br>

如果你的电脑运行着Windows，且运行内存为16GB，则不推荐通过安装虚拟机来进行编译操作。

3.你的电脑需要350GB以上的硬盘空间

> 如果你需要进行多次编译，则最好预留500GB以上的硬盘空间。<br>

4.你的电脑需要运行Ubuntu 20.04（wsl，虚拟机，直接运行的都可以）

5.你的电脑具有良好的网络环境

> 建议租一个云服务器，会更方便一些。<br>

**以下命令均需要在终端下运行。**

## 编译教程

这里以编译Lineage OS 19.1为例：

### 一、准备编译环境

#### 1.更新软件源

```
sudo apt-get update && apt-get upgrade -y
```

#### 2.安装依赖

```
sudo apt-get install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev libelf-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-gtk3-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

#### 3.设置git相关信息

```
git config --global user.name "在此处替换成你的名字"
git config --global user.email "在此处替换成你的邮箱"
```

#### 4.安装repo

```
mkdir -p ~/.bin
PATH="${HOME}/.bin:${PATH}"
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo
chmod a+rx ~/.bin/repo
```

### 二、拉取相关代码

#### 1.创立工作区和工作文件夹

```
screen -S sync
mkdir LineageOS && cd LineageOS
```

#### 2.拉取Lineage OS源码

```
repo init -u https://github.com/LineageOS/android.git -b lineage-19.1
repo sync
```

> 如果硬盘空间比较紧张，可以通过在第一行命令后加上 ```--depth=1```来节省空间。<br>

同时，在第一行命令输入完成后，你可以通过把镜像源换成清华镜像站来加快同步速度：

```
git config --global url.https://mirrors.bfsu.edu.cn/git/AOSP/.insteadof https://android.googlesource.com
```

>

#### 3.同步设备相关代码

```
git clone https://github.com/wbs306/device_xiaomi_mt6895-common.git device/xiaomi/mt6895-common
git clone -b lineage-19.1-oss https://github.com/YuKongA/device_xiaomi_rubens.git device/xiaomi/rubens
git clone https://github.com/PixelExperience/device_mediatek_sepolicy_vndr device/mediatek/sepolicy_vndr
git clone https://github.com/YuKongA/vendor_xiaomi_rubens.git vendor/xiaomi/rubens
git clone https://github.com/wbs306/kernel_xiaomi_mt6895.git kernel/xiaomi/mt6895
```

#### 4.修补相关补丁

```
cd ~/LineageOS/frameworks/opt/telephony
git fetch https://gerrit.pixelexperience.org/frameworks_opt_telephony refs/changes/76/15476/1 && git cherry-pick FETCH_HEAD
cd ~/LineageOS/frameworks/opt/net/ims
git fetch https://gerrit.pixelexperience.org/frameworks_opt_net_ims refs/changes/18/15318/1 && git cherry-pick FETCH_HEAD
git fetch https://gerrit.pixelexperience.org/frameworks_opt_net_ims refs/changes/17/15317/1 && git cherry-pick FETCH_HEAD
cd ~/LineageOS
```

> 如果你编译的是像Pixel Experience这些已经合并补丁的ROM，则不需要进行这一步。<br>

#### 5.修补必要的文件

从13.0.22的官方包里提取vendor.img，odm.img，然后把这两个文件上传到工作目录里的device/xiaomi/rubens/prebuilts文件夹里。

#### 6.开始编译

```
source build/envsetup.sh
lunch lineage_rubens-userdebug
mka bacon
```

此时按下Ctrl+A+D可以脱离工作区，你也可以随时通过```screen -R sync```来查看编译进度。

当编译完成时，在工作目录下的out/target/product/rubens找到编译完成的包以及md5校验文件。可以通过TWRP来刷入刷机包。

> 如果不进行第五步，可以通过以下命令只编译system分区与product分区：<br>

```
source build/envsetup.sh
lunch lineage_rubens-userdebug
mka systemimage && mka productimage && cd$OUT && zip dsu.zip system.img product.img
```

这样下来你会得到一个dsu.zip，里面有两个img文件，你可以通过DSU Sideloader来直接加载系统，也可以直接用fastboot刷入对应分区。

>
