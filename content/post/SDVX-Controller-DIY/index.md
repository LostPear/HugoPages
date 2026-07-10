---
title: "SDVX 手台自制教程"
date: 2024-05-19 16:36:00
lastmod: 2025-02-07 15:17:29
tags:
  - 教程
categories:
  - 教程
slug: "SDVX-Controller-DIY"
---
> 突然就想做一个SDVX的手台玩了，第一次搞这些踩了不少坑，就在这里简单写一下自己的制作经验。<br>
在开始制作之前，请确认你自己有足够的耐心和一定的动手能力，一般来说我更建议买现成的商业手台，虽然要比自己做贵一些，但是更方便，体验也更好一些。<br>
在制作过程中，我参考了以下项目和文章，在这里对各位大佬表示感谢：<br>
[简单自制SDVX手台（控制器）](https://www.bluesdawn.top/sdvx-diy/index.html)<br>
[lizardbeans/sdvx-diy](https://github.com/lizardbeans/sdvx-diy)<br>
[FreezeRasis/sdvx-diy-pcb](https://github.com/FreezeRasis/sdvx-diy-pcb)<br>
[Sirius-RX/SDVX-SP_Con](https://github.com/Sirius-RX/SDVX-SP_Con)<br>
并且特别感谢[@Huang_xuan](http://www.coolapk.com/u/1704149) 帮助修改固件和组装过程的各种答疑解惑！<br>

## 一、材料准备

我做手台用到的材料如下：

1.开发板：用的是[这个仓库](https://github.com/FreezeRasis/sdvx-diy-pcb)里的方案，需要下载嘉立创EDA，然后把仓库里的json文件导入后下单，具体使用方法仓库里面写的已经很清楚了。嘉立创每个月可以免费下单两次PCB，在[下单平台](https://www.jlc.com/newOrder/#/collectCoupons)里领券之后再确认订单。开发板需要买Pro Micro，芯片是ATMEGA32U4。

![][1]

2.外壳：直接去淘宝一家名叫“多美歌外壳”做的雪弗板，他们那里有现成的方案，不要图案的话可以便宜20块。

![][2]

多美歌的板子厚度是10mm，虽然会有点厚，但是在不需要加装亚克力板的情况下，实测对安装没什么影响。或者你也可以用外壳的CAD文件自己定制。CAD文件你可以在[这个仓库](https://github.com/Sirius-RX/SDVX-SP_Con)里找到：

3.按键：用的是淘宝“喵呜手台店”的按键，优点就是便宜而且自带弹簧。

![][3]

体验想要更好的话可以换星韵家的（淘宝“星韵工作室”）或者三和按键（特别贵，但据说手感也非常好），弹簧也需要自己另外买。

4.微动：霍尼韦尔V15S05-1EZ050-K01，一共需要买7个，想要体验更好就上欧姆龙的。

![][4]

5.旋转编码器和旋钮：需要买磁电式NPN的，工作电压5-24V，脉冲600ppr，AB两相，需要买两个。

![][5]

旋钮就选一般的全铝旋钮就可以。规格直径30mm高25mm，也是买两个。

![][6]

6.其他的一些小零件：

（1）M5*25 自攻螺丝 19个，用来固定外壳；

（2）M3*16 平头螺丝6个，用来固定旋转编码器；

（3）5V蓝色迷你LED灯5个，红色2个，红色的需要安装到FX-L和FX-R两个按键上，蓝色的安装到其他按键上，如果你不需要灯效就不需要买；

（4）带灯按键微动线7条，用来连接按键到板子上，淘宝搜对应关键词就能找到了；

（5）XH2.54 公头单头端子线 2条，用来连接旋转编码器到板子上

（6）XH2.54直针座 4P 9个，焊接到板子上需要；

（7）Type-C/MicroUSB延长线，具体看你买的板子用的接口；

（8）防滑脚垫4个（可选）；

7.需要的工具：

（1）焊接相关的东西（烙铁，锡丝，松香等等）；

（2）一把十字头螺丝刀；

（3）一套六角扳手；

（4）一把剪线钳；

（5）一把热熔胶枪，用于固定PCB板；

## 二、组装过程

### 1.焊接

分别需要焊接板子和旋转编码器的线头。板子的左上角已经给开发板预留了位置，把Pro Micro接口朝外放在左上角的位置，然后转到PCB板的背面，把开发板的针脚焊接在板子上。剩余的接口都是给端子座预留了，在每个位置上用类似的方法焊接一个上去。焊接的教程可以看[这个视频](https://www.bilibili.com/video/BV1hc41167iA)。

![][7]

接下来是线头的焊接。按照PCB板的引脚定义，从左到右的顺序应该为：5V，OUTA，OUTB，GND。旋转编码器上已经写清楚了不同颜色的线对应的功能，把他们按照这个顺序焊接到端子线上，焊接好后记得分别缠上一圈电工胶带。焊接线头可以参考[这个教程](https://www.bilibili.com/video/BV1Vo4y1q71S)。

![][8]

### 2.固件上传

需要下载Arduino IDE，把焊接好后的板子连接到电脑上，点击“Tools”选项卡,“Board"一栏选择“Arduino Leonardo”，端口选择你板子的端口。

![][9]

在[这个仓库](https://github.com/Huangxuan512/sdvx-diy-pcb/tree/master/code%20Leonardo/Arduino%20Leonardo/leovx)里下载固件，双击leovx.ino在Arduino中打开，然后点击左上方的右箭头键，把固件上传到开发板。

![][10]

### 3.组装

(1)按键部分

一个按键一般会有这几部分：按键本体，套在按键外面的壳子，微动支架，以及固定壳子的螺母（不知道怎么叫准确，姑且先这么叫吧）。

![][11]

先把按键扣在雪弗板上对应的位置，

![][12]

然后再把壳子扣在按键的背面，

![][13]

把螺母拧上去拧紧，

![][14]

按键部分就组装完成了。

把微动固定在微动支架上，支架上有两个凸出来的地方，和微动是对应的，按照这个地方安装微动。

![][15]

![][16]

把灯固定在微动支架的下端，注意灯上长的一端是正极，这个一会接线的时候需要注意。把安装好的微动支架拧到按键背面，这样按键部分就安装好了。其他的按键也用类似的办法装上去；

![][17]

(2)旋转编码器部分

旋转编码器上有6个螺丝孔，把其中的任意三个对准雪弗板正面预留的空位上，然后用M3*16的螺丝拧上去。

![][18]

你可以看到旋转编码器会露出一截轴，用六角螺丝刀，把旋钮固定到轴上面。

![][19]

(3)接线部分

PCB板上已经写好了每个插口对应的部分，前面已经焊接好了旋转编码器的线，直接插到对应位置就可以了。对于按键部分，我简单做了一个图展示怎么连接：

![][20]

> PS：微动支架上不严格区分正负极。也就是说只要确保把PCB板上负责正负极的两个引脚接到支架两端就可以了。<br>

(4)外壳部分

板子上已经给了组装时候需要用到的孔位了，先把板子按照形状拼好，然后把M5*25的自攻螺丝放到孔里，直接使劲拧进去就行，注意区分预留USB孔位的那一面，不要把螺丝拧到这里。把脚垫贴在手台底部的四个角。

(5)其他部分

用热熔胶枪，把板子固定到背面FX-L和FX-R两个按键中间的位置。然后把延长线一端接到板子上，另一端从壳子上预留的孔位绕出来。先暂时不要把正面合盖。

![][21]

### 4.软件测试

在spice里绑定对应的键位，并测试功能是否正常，注意旋钮需要另外在“Analogs”下绑定。如果发现旋钮左右旋转方向是反过来的，需要勾选“Invert Axis”选项。灵敏度推荐调整为0.25。

![][22]

> 如果你发现在这个菜单下，旋钮高速旋转时并不能被正确识别，请不要慌张，进游戏后在服务菜单里再测试一下，只要游戏里旋转正常就没什么问题。<br>

测试没有问题之后就可以合盖安装了。

至此，你已经做好了一个可以正常游玩的SDVX手台，可以愉快玩耍啦~

  [1]: https://images.shojola.top/Articles/SDVX-Controller-DIY/1.jpg

  [2]: https://images.shojola.top/Articles/SDVX-Controller-DIY/2.jpg

  [3]: https://images.shojola.top/Articles/SDVX-Controller-DIY/3.jpg

  [4]: https://images.shojola.top/Articles/SDVX-Controller-DIY/4.jpg

  [5]: https://images.shojola.top/Articles/SDVX-Controller-DIY/5.jpg

  [6]: https://images.shojola.top/Articles/SDVX-Controller-DIY/6.jpg

  [7]: https://images.shojola.top/Articles/SDVX-Controller-DIY/7.jpg

  [8]: https://images.shojola.top/Articles/SDVX-Controller-DIY/8.jpg

  [9]: https://images.shojola.top/Articles/SDVX-Controller-DIY/9.jpg

  [10]: https://images.shojola.top/Articles/SDVX-Controller-DIY/10.jpg

  [11]: https://images.shojola.top/Articles/SDVX-Controller-DIY/11.jpg

  [12]: https://images.shojola.top/Articles/SDVX-Controller-DIY/12.jpg

  [13]: https://images.shojola.top/Articles/SDVX-Controller-DIY/13.jpg

  [14]: https://images.shojola.top/Articles/SDVX-Controller-DIY/14.jpg

  [15]: https://images.shojola.top/Articles/SDVX-Controller-DIY/15.jpg

  [16]: https://images.shojola.top/Articles/SDVX-Controller-DIY/16.jpg

  [17]: https://images.shojola.top/Articles/SDVX-Controller-DIY/17.jpg

  [18]: https://images.shojola.top/Articles/SDVX-Controller-DIY/18.jpg

  [19]: https://images.shojola.top/Articles/SDVX-Controller-DIY/19.jpg

  [20]: https://images.shojola.top/Articles/SDVX-Controller-DIY/20.jpg

  [21]: https://images.shojola.top/Articles/SDVX-Controller-DIY/21.jpg

  [22]: https://images.shojola.top/Articles/SDVX-Controller-DIY/22.png
