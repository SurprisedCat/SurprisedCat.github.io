---
layout: post
title:  "新玩具--树莓派3B+"
categories: Raspberry
tags:  Raspberry
author: SurprisedCat
---

* content
{:toc}

## 树莓派Raspberry Pi ##

Raspberry Pi(中文名为“树莓派”,简写为 RPi，或者 RasPi/RPi)是为学生计算机编程教育而设计，只有信用卡大小的卡片式电脑，其系统基于 Linux。

树莓派由注册于英国的慈善组织“Raspberry Pi 基金会”开发，Eben·Upton /埃·厄普顿为项目带头人。2012 年 3 月，英国剑桥大学埃本·阿普顿（Eben Epton）正式发售世界上最小的台式机，又称卡片式电脑，外形只有信用卡大小，却具有电脑的所有基本功能，这就是Raspberry Pi 电脑板，中文译名"树莓派"！这一基金会以提升学校计算机科学及相关学科的教育，让计算机变得有趣为宗旨。基金会期望这 一款电脑无论是在发展中国家还是在发达国家，会有更多的其它应用不断被开发出来，并应用到更多领域。$^{[1]}$
<!--excerpt_separator_here-->

### Raspberry Pi 3 Model B ###

树莓派3B于2016年2月29日发布，从外观上来看，Raspberry Pi 3B和2B没有太大的区别，同样是85.6 x 56.5 mm x 17mm（重量45g略有增加）。然而在内在配置上提升很可观的，具体配置包括：$^{[2]}$

* Broadcom BCM2837 SoC, with quad-core ARM Cortex-A53 1200 MHz processor
* 博通 BCM2847 芯片，集成了ARM Cortex-A53四核处理器，1200MHz主频。
* VideoCore IV dual-core 400 MHz GPU
* VideoCore IV双核400MHz GPU
* 1 GB SDRAM - shared by the GPU and CPU
* 1GB SDRAM -由GPU和CPU共享
* MicroSD card slot for boot and storage
* 启动和存储的MicroSD card卡槽
* 4 x USB 2.0 ports (via on-board 5 port hub)
* 4个USB2.0端口
* RJ45 10/100 MBit/s Ethernet port
* RJ45 10/100 MBit/s 以太网口
* HDMI and Composite video, audio through TRRS jack
* HDMI和音频TRRS复合段端子
* 40 pin GPIO Interface connector
* 40引脚的GPIO连接柱
* CSI (camera) and DSI (display) connectors
* CSI 和 DSI 接口
* 4 squarely positioned mounting holes
* 4个角的固定孔

此外，Raspberry Pi 3B还集成了802.11n和Bluetooth4.1无线功能，这算是功能上最新颖的扩展了。

## 型号与版本对照 ##

![树莓派型号对比图]( /assets/raspberrypi-version-compare.png)$^{[3]}$

## Raspberry Pi 3B 电气参数 ##

### 电源供电能力 ###

The board takes fixed 5V input, (with the 1.2 V core voltage generated directly from the input using the internal switch-mode supply on the BCM2835 die). This permits adoption of the micro USB form factor, which, in turn, prevents the user from inadvertently plugging in out-of-range power inputs; that would be dangerous, since the 5 V would go straight to HDMI and output USB ports, even though the problem should be mitigated by some protections applied to the input power: The board provides a polarity protection diode, a voltage clamp, and a self-resetting semiconductor fuse.

Premier Farnell recommend the following power supplies:

Model A: 5 V DC, 500-700 mA
Model B: 5 V DC, 700-1500 mA

Power consumption of the Raspberry Pi device is

Board A: 5 V, 500 mA (2.5 W) without any devices connected (e.g. USB, Ethernet, HDMI)
Board B: 5 V, 700 mA (3.5 W) without any devices connected (e.g. USB, Ethernet, HDMI) (Is this correct? The 700 mA is only required if "using networking and high-current USB peripherals".)
You will need to provide a power supply that can provide enough current to power the device plus any connected peripherals, and taking into account inefficiencies of the supply itself and the cable between the power supply and Raspberry Pi. The community advises opting for a power supply that can supply **at least 1 A** if using USB peripherals or Pi plates that draw more than a few tens of milliamperes of current.

As the 5 V rail is brought out in the GPIO pins, you can power the Raspberry Pi from there too. You should mind however, that those are behind the power protection circuitry, so you should provide your own.

It is possible to power the Raspberry Pi from a powered USB hub the Raspberry Pi controls, but only on 'dumb' devices, that allow the port to supply the full current without waiting for the USB device to ask for it. As the power input of the Raspberry Pi doesn't have its data leads connected, there is no chance for a communication loop of some sorts.

POE (power over Ethernet) is currently **not available** for the Raspberry Pi (but nobody stops you from taking your soldering iron and doing it yourself - mind though that the Ethernet jack on the board is a 'magjack' - http://www.sparkfun.com/datasheets/Prototyping/MagJack.pdf - which means that the usual 'dumb or passive PoE' power pins 47 and 78 are **not** wired through to the board. So this is not an entirely trivial exercise).

Back-Powering; (powering the Raspberry Pi from a USB hub through the uplink/data port, single cable) Back powering is possible on the Raspberry Pi, but not advisable. Revision 1.0 boards have to be modified to back power, this is due to the 140 mA "polyfuses" that are installed in the USB port circuit. Revision 1.1 boards do not need modifications to back-power, they have replaced the polyfuses with 0 ohm resistors in their place. Revision 2.0 boards do not need modification, they have neither resistors nor polyfuses. It is advised that short (12" (.3 meter) or less) USB cables be used for back-powering a Raspberry Pi. Cable resistance plus connector resistance can quickly reduce operating voltages below the proper range (5.25 V to 4.75 V). But do note that if you do not power the Raspberry Pi in the "official manner", that is through its micro-USB port, but use any alternative way (such as through the GPIO header, the test points TP1 and TP2), but also by back-powering it, you are actually bypassing the Raspberry Pi's input polyfuse protection device! This can have extreme consequences if ever you manage to put more than 6 V on the Raspberry Pi, even for a very short period. As this causes the overvoltage device D17 on the Raspberry Pi to trigger and short the 5 V supply! Without the polyfuse limiting the current through D17, it will burn out, probably melting the Raspberry Pi's enclosure with it, (if you have any) and possibly causing a fire-hazard. It will probably also create a permanent short of the 5 V supply! So be warned, and if you use back power make sure your hub or its PSU has a fuse to prevent this from happening. If not, add your own fuse.

### Power supply problems ###

There have been a number of problems reported that seem to be caused by inadequate power, this is an attempt to explain what is needed and the consequences of not having enough power.

The power required by the Pi will vary depending on how busy it is and what peripherals are connected.

* Running a GUI will take more power.
* The USB devices and Ethernet connection will take power.
* Running the GPU will take extra power.

This means that it's difficult to say exactly how much power is needed. People have reported current requirements of between 300 mA and 550 mA. But it could in reality take more, especially for short periods. A simple multimeter will not show short surges on the power requirement. A surge in the power requirement for a few milliseconds will not be detectable by a meter but will be enough to cause problems. If the board does not get enough power the voltage will drop. If it drops enough parts of the system will run unreliably because data can get corrupted. The **USB IC** runs on 5 V and handles the USB and Ethernet ports so it's likely that this will be the first thing to fail. Problems seen are unreliable Ethernet connection and unreliable operation of the Keyboard and/or mouse.

Each of the two USB ports on the Pi has a polyfuse rated at 140 mA, so any connected USB devices should draw less than this amount of current. In addition the polyfuse will cause a significant voltage drop, so that USB devices get less voltage than is available on the Raspberry Pi itself, sometimes up to half a volt less (maybe more if the fuse has recently been hot). For regular "low power" USB devices this doesn't cause a problem as they are designed to work with voltages as low as 4.4 volt. This isn't the case however with some USB devices such as Wi-Fi dongles which may need 4.75 volt, and are also known to draw more than 150 mA when configured and active. Because of the problems these polyfuses caused Raspberry Pi's produced after August 25, 2012 have the USB polyfuses F1 & F2 removed (replaced with shorts).

The microUSB input port also has a 1.1 A polyfuse (700 mA "hold current") which may also have enough resistance (although much smaller than the 140 mA fuses) to cause a significant voltage drop on the board, even below its 1.1 A total current.

A extended explanation of the consequences of the use of these polyfuses can be found here Polyfuses explained

There are several reasons why the power to the board may be inadequate:

The PSU may not deliver enough power. Although the maximum power requirement is said to be 700 mA, that is with no peripherals connected (USB, Ethernet etc), so a 1000 mA PSU should be regarded as a minimum. This allows some leeway in case the power supply cannot deliver its full power without the voltage dropping.

The PSU is not regulated.

The cable connecting the PSU to the Pi may not be good. People have reported cables with 4 ohms resistance on the power connections. At 500 mA drain this would reduce a 5 V supply to 3 V.

If the PSU is unregulated it can also output too high a voltage, which may trigger the overvoltage device in the Raspberry Pi, which will temporarily short the 5 V to ground, this will then "blow" polyfuse F3, which will take several days to recover from. Meanwhile (possibly with another PSU) the Raspberry Pi might not get enough power because the (partly) blown polyfuse is consuming some of the power. The solution is when this happens to ways a few days to give the polyfuse time to recover before attempting to use the better PSU. If you suspect a blow polyfuse, measure the voltage across F3, which should be less than 0.05 volt.
How can I tell if the power supply is inadequate?

### Common symptoms of an inadequate power supply ###

* Unreliable Ethernet or keyboard operation, especially if it's OK at first but not when the GUI is started.
* SD card errors at start up seems to be another symptom of poor power.

If you think you have a problem with your power supply, it is a good idea to check the actual voltage on the Raspberry Pi circuit board. Two test points labelled TP1 and TP2 are provided on the circuit board to facilitate voltage measurements.

Use a multimeter which is set to the range 20 volts DC (or 20 V =). You should see a voltage between 4.75 and 5.25 volts. Anything outside this range indicates that you have a problem with your power supply or your power cable, or the input polyfuse F3. Anything inside, but close to the limits, of this range may indicate a problem.

### GPIO口电气信息 ###

In addition to the familiar USB, Ethernet and HDMI ports, the Raspberry Pi offers the ability to connect directly to a variety of electronic devices. These include:

>Digital outputs: turn lights, motors, or other devices on or off
>Digital inputs: read an on or off state from a button, switch, or other sensor
>Communication with chips or modules using low-level protocols: SPI, I²C, or serial UART
Connections are made using GPIO ("General Purpose Input/Output") pins. Unlike USB, etc., these interfaces are **not "plug and play"** and require care to avoid miswiring. The Raspberry PI GPIOs use **3.3V** logic levels, and can be damaged if connected directly to 5V levels (as found in many older digital systems) without level-conversion circuitry.

Note that **no analogue input or output is available**. However, add-on boards such as the Rpi Gertboard provide this capability.$^{[4]}$

## 40pin引脚对照表 ##

![B+图](/assets/300px-B_plus_hdr_sm.jpg)$^{[5]}$
![40pin引脚](/assets/300px-Pi-GPIO-header.png)$^{[5]}$

## 参考文献 ##

[1][http://wiki.jikexueyuan.com/project/raspberry-pi/overview.html](http://wiki.jikexueyuan.com/project/raspberry-pi/overview.html)

[2][https://elinux.org/RPi_Hardware](https://elinux.org/RPi_Hardware)

[3][http://shumeipai.nxez.com/raspberry-pi-version-compare](http://shumeipai.nxez.com/raspberry-pi-version-compare)

[4][https://elinux.org/RPi_Hardware](https://elinux.org/RPi_Hardware)

[5][https://elinux.org/RPi_Low-level_peripherals](https://elinux.org/RPi_Low-level_peripherals)