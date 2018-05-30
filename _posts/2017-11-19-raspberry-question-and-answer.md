---
layout: post
title:  "树莓派常见问题解决（有问题时常更新）"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

本页面记录了使用树莓派的过程中遇到的一些问题和解决方案。

1. 安装好系统后没有显额外示器，新版本树莓派默认关闭ssh服务器，无法连接上树莓派。
> 在SD卡的/boot中，添加一个名为“ssh”的空文件，注意不用使用windows自带的文本编辑器动它！
2. 树莓派连接上显示器之后没有反应。
> 修改sd卡/boot分区中的config.txt文件。
```shell
hdmi_force_hotplug=1
config_hdmi_boost=4
hdmi_group=2
hdmi_mode=9
hdmi_drive=2
hdmi_ignore_edid=0xa5000080
disable_overscan=1
```
> 解释：  
hdmi_force_hotplug：强制使用HDMI输出（强行认为HDMI口已经插入了设备）  
config_hdmi_boost：HDMI信号增强。  
hdmi_group、hdmi_mode：决定分辨率。group=2, mode=9 代表800×600 @ 60Hz。（参考：http://elinux.org/RPiconfig）  
hdmi_drive：强制音频输出到HDMI口（注意，仅适用于带音频的HDMI-VGA转换器！！如果想让音频从模拟输出，则去掉此项！！）  
hdmi_ignore_edid：强行按hdmi_group和hdmi_mode规定的分辨率输出。不检测显示器自身的分辨率。  

3. 图形性能如何？
> GPU支持OpenGL ES 2.0、硬件加速的OpenVG，和高至1080p30fps的H.264硬件解码。
GPU的通常计算能力达到1Gpixel/s, 1.5Gtexel/s 或 24 GFLOPs，并且提供一系列材质渲染过滤与DMA功能。
相比较来看，树莓派的图形性能基本上与初代Xbox等同。树莓派的总体性能也许和300MHz的奔腾2接近，不过图形能力是远远超越那个时代的。

<!--excerpt_separator_here-->

4. 键盘字符打不出来问题。
> 树莓派默认是英国键盘，字符与美式键盘有些地方不一样，所以需要一些设置。  

```shell
sudo raspi-config
choose 4. Localisation Options >> I3 Change keyboard Layout >> Gernic 101-Key PC >>  Other >> English (US) 
然后一路选择默认的配置就可以了
```

## 参考文献 ##