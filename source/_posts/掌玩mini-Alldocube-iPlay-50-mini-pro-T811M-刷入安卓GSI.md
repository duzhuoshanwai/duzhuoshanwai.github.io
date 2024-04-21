---
title: 掌玩mini(Alldocube iPlay 50 mini pro/T811M)刷入安卓GSI和简单的体验报告
date: 2024-04-20 07:00:40
tags:
---

## 什么是GSI

以下内容来自于 [AOSP文档](https://source.android.com/docs/core/tests/vts/gsi?hl=zh-cn)

> 通用系统映像 (GSI) 是指已针对 Android 设备调整配置的系统映像。GSI 被视为“纯 Android”实现，它包含未经修改的 Android 开源项目 (AOSP) 代码，任何搭载 Android 9 或更高版本的 Android 设备都能顺利运行这种代码。
>
> GSI 用于运行 VTS 和 CTS-on-GSI 测试。为确保运行最新版 Android 的设备正确实现供应商接口，您需要将 Android 设备的系统映像替换为 GSI，然后使用供应商测试套件 (VTS) 和兼容性测试套件 (CTS) 来测试设备。

## 掌玩mini的简单介绍

- 8.4英寸全贴合屏幕 1920*1200 虽然显示效果不咋地用来读读文档够用
- Helio G99 上古soc几百块要什么自行车
- 8+128 要什么自行车
- sim卡sd卡
- 有3.5mm耳机孔 单扬声器垃圾水平
- 5000mAh 18w(实测根本没有)
- 做工有点差边框可能有异响 论坛反馈可能有断触情况需要自行拆机垫胶带

## 刷入GSI

**注意!!!** 进行一下操作前需要刷入官方国际版20230818固件，否则可能会无限重启

### 需要准备的工具的链接

[spflashtools](https://spflashtools.com/) 降级刷入工具

[mtk_usb_all](https://androidmtk.com/download-mtk-usb-all-drivers) 联发科驱动

[xda论坛老哥移植的第三方recovery](https://xdaforums.com/t/recovery-unofficial-beta-twrp-3-7-1-for-alldocube-iplay-50-mini-pro.4658429/) 可选

[platform-tools](https://developer.android.com/tools/releases/platform-tools) 需要使用到的adb和fastboot工具

### 刷入官方国际版固件20230818

**确保已经安装了联发科驱动!!!**

1. 下载固件包并解压 [官网](https://www.alldocube.com/en/firmware/alldocube-iplay-50-mini-prot811m-firmware-download/) [个人网盘分流](https://download.saint1337.top/tools/t811m/iPlay50miniPro(T811M)_EN_20230818.rar)

2. 将设备关机

3. 打开spflashtool 按照图中设置Download-XML为 ./iPlay50miniPro(T811M)_EN_20230818/download_agent/flash.xml

   将Download Only改为Firmware Upgrade

![](https://s2.loli.net/2024/04/20/HqS1MjPx3wLdfQr.png)

4. 点击绿色箭头那个Download 然后把关机状态的平板连上电脑 等待完成

### 刷入GSI

**建议添加adb和fastboot命令到PATH**

1. 重启进入系统后连击版本号进入开发者模式打开 USB调试和OEM解锁

2. **解压 `lineage-21.0-20240217-UNOFFICIAL-arm64_bgN.img.xz` 得到img文件**

3. 在解压好的platform-tools文件夹下打开命令行

    `adb reboot bootloader` 在平板上确认后重启到fastboot模式

    `fastboot flashing unlock` 按照平板上的提示按下音量减 解锁 **这一步会清空所有数据!!!注意备份**

    `fastboot --disable-verity --disable-verification flash vbmeta_a vbmeta.img` 刷入vbmeta并**禁用Android Verified Boot (AVB)**

    `fastboot --disable-verity --disable-verification flash vbmeta_b vbmeta.img` 同上

4. 重启到fastbootd后操作

    `fastboot reboot fastboot` 从fastboot重启到**fastbootd模式**

    `fastboot set_active a` 可能需要切换到分区a

    `fastboot delete-logical-partition product_a` 删除分区 product_a 这个分区是出厂自带的

    `fastboot erase system_a` 擦除分区 system_a

    `fastboot flash system_a lineage-21.0-20240217-UNOFFICIAL-arm64_bgN.img` 刷写上面解压的img到 system

    `fastboot flash vendor_boot OFOX-IP50MP-BETA.img` 刷入第三方rec (可选)

    `fastboot flash vendor mod-vendor.img` 刷入修改好的vendor

    `fastboot flash boot magisk_patched-27000_l2O2k.img` 刷入修补好的boot 用于获取root权限

    `fastboot erase userdata` 擦除 userdata分区

    `fastboot erase metadata` 擦除metadata分区

5. 重启系统 `fastboot reboot` 等待开机

### 开机后的一些准备工作

- 安装Magisk [https://magiskmanager.com/downloading-magisk-manager](https://magiskmanager.com/downloading-magisk-manager)

  ![1000071653.jpg](https://s2.loli.net/2024/04/21/IsNLwlW4M6xSC2k.jpg)

- 修改dpi `adb shell wm density 300`

- 修复耳机 

  `adb.exe shell`

  `su` 切换到root用户 需要在平板上确认允许

  `setprop persist.sys.overlay.devinputjack true` 然后重启设备

 **enjoy !**
