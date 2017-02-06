---
title: 组建自己的工作站
date: 2017-02-03 10:38:12
tags: 
---

苹果的`Macbook Pro`的`8g`内存（2016年发布会也没有推出32g版）运行一些需要集群环境的程序比较吃力，例如`Spark`、`Openshift`、`Fabric8`。调研了下配置较高的云服务器价格又略贵，所以萌生了自己组装一台工作站的想法。

## 需求

1. 内存64g，且易于扩展
2. CPU 核心多，可以跑虚拟机模拟集群环境。
3. 可以黑苹果，个人比较习惯Mac系统
4. PCI-E x16 接口丰富

## 硬件环境

项目 | 名称
-----|-----
CPU | Xeon E5 2683 v3 * 2
主板 | Intel S2600CW2R
内存 | 三星 DDR4 ECC 32g * 2
显卡 | Asus R9 270
网卡 | TP-LINK TL-WDN4800
电源 | 海韵（Seasonic）额定620W M12II-620 电源
机箱 | 银欣乌鸦3
散热器 | 九州风神大霜塔 * 2
键盘 | HHKB PRO2
硬盘 | SSD 128g + 4TB 黑盘

### 电源

![](/images/power-1.jpg)

![](/images/power-2.jpg)

### CPU

### 内存

![](/images/memory.jpg)

### 主板

### 显卡

### 机箱

### 散热器

![](/images/radiator.jpg)

### 键盘

![HHKB-Pro2](/images/hhkb-keyboard.jpg)

HHKB没有实体方向键，在VIM下没有什么影响，在Mac下也有一些快捷键可以代替方向键，总之习惯后非常好用。

快捷键 | 说明
-------|-----
Ctrl+p | shell中上一个命令,或者 文本中移动到上一行
Ctrl+n | shell中下一个命令,或者 文本中移动到下一行
Ctrl+r | 往后搜索历史命令
Ctrl+s | 往前搜索历史命令
Ctrl+f | 光标前移
Ctrl+b | 光标后退
Ctrl+a | 到行首
Ctrl+e | 到行尾
Ctrl+d | 删除一个字符,删除一个字符，相当于通常的Delete键
Ctrl+h | 退格删除一个字符,相当于通常的Backspace键
Ctrl+u | 删除到行首
Ctrl+k | 删除到行尾
Ctrl+l | 类似 clear 命令效果

## macos sierra

    这里需要说明的是黑苹果安装是不被苹果允许的

### 下载基础工具

1. 从`App Store`中下载`macos sierra`

2. ![Download-macos](/images/download-macos-sierra.png)

3. 下载[Unibeast](/assets/UniBeast-7.0.1.zip)，用于制作U盘镜像。

4. 下载[MultiBeast-Sierra-Edition](/assets/MultiBeast-Sierra-Edition-9.0.1.zip)

### 制作USB启动工具

1. 格式化USB设备（U盘）

    ![](/images/formatter-usb-boot-device.png)

    ![](/images/formatter-usb-boot-device2.png)

2. 使用Unibeast制作U盘镜像

    ![](/images/selected-usb.png)

    ![](/images/selected-installation-type.png)

    ![](/images/bootloader-configuration.png)

    如果主板支持UEFI引导，则选择第一个，不支持则选择第二个。

    ![](/images/graph-device.png)

    选择显卡驱动

    ![](/images/copying-files.png)

    这个地方很慢，要耐心等..

### BIOS设置

### 安装macos sierra

正常的macos安装过程，没有特别的异常，不多做说明

### MultiBeast设置引导

![](/images/multibeast.png)

勾选好需要的驱动程直接点击安装就好了， 整个过程比较顺利

### 常见问题和优化
