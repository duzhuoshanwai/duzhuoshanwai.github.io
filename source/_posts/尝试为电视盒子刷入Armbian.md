---
title: 尝试为电视盒子刷入Armbian
date: 2024-02-24 09:52:06
---

## 准备

* 运营商的电视盒子一只 以晶晨芯片的CM311-1为例 [查看目前可用的设备列表](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/README.cn.md)

* 双公头usb线 没有现成的可以用两根不要的线剥开对接四芯自己制作

* 一个闲置的空u盘

* （可选)晶晨盒子短接神器 没有的话其实用镊子短接也可以

  ![](https://s2.loli.net/2024/02/24/m8dgyULcaBI5ehl.jpg)

* [晶晨烧录工具Amlogic_USB_Burning_Tool](https://download.saint1337.top/tools/%E8%BF%90%E8%90%A5%E5%95%86%E7%9B%92%E5%AD%90Armbian/)

* [选择合适的安卓底包用来开启adb](https://github.com/ophub/kernel/releases/tag/tools)   [部分底包分流](https://download.saint1337.top/tools/%E8%BF%90%E8%90%A5%E5%95%86%E7%9B%92%E5%AD%90Armbian/%E5%AE%89%E5%8D%93%E5%BA%95%E5%8C%85/)

* adb工具 [xda的安装教程参考](https://www.xda-developers.com/install-adb-windows-macos-linux/) 或者使用开心电视助手替代 [链接]()

* [balena etcher](https://etcher.balena.io/) 或者 [rufus](https://rufus.ie/zh/) 用来写入Armbian镜像到u盘

## 刷入底包

![Snipaste_2024-02-24_12-19-51.png](https://s2.loli.net/2024/02/24/4ctyfg5TbmIhWl8.png)

打开晶晨刷机工具，设置→导入镜像。

![Snipaste_2024-02-24_12-22-46.png](https://s2.loli.net/2024/02/24/MuJr4amDBENfAIq.png)

点击开始后准备接入线。

![1000070790.jpg](https://s2.loli.net/2024/02/24/VwXUPdhJ7rEM4pG.jpg)

关闭电源！！！ 插入刷机神器，双公线一头插入盒子靠近网口的那个usb口，另一头接入电脑usb口（2.0的可能好一点）。先在烧录工具上点开始，接入线，然后打开盒子上的电源开关等待烧录工具的提示即可。

## 刷入Armbian镜像到U盘

到[release](https://github.com/ophub/amlogic-s9xxx-armbian/releases)中选择适合自己硬件的固件 使用Balena Etcher刷入

刷写之后的U盘，在设置盒子从U盘启动前先不要插入盒子。

![Snipaste_2024-02-24_18-03-10.png](https://s2.loli.net/2024/02/24/rSdYsG67kZhORzo.png)

## 使用ABD设置电视盒子从usb启动

### 开启ADB

大部分安卓刷机包都已经设置了ADB调试。如果没有的话需要连点版本号进入开发者选项自行开启。

### 使用命令行的ADB命令

```
adb connect 192.168.2.162
adb shell reboot update #输入命令之后再插入U盘！！！
```

### 使用开心电视助手

在首页输入电视盒子的ip和默认端口5555→调试→从U盘/SD卡启动 ！！！点击之后再插入U盘！！！

不要在安卓系统下插入U盘！！！可能会导致U盘中文件权限损坏，修复需要重新刷入Armbian镜像。

![Snipaste_2024-02-24_17-48-47.png](https://s2.loli.net/2024/02/24/oTN5jRtLBuQKPCM.png)

![Snipaste_2024-02-24_17-49-02.png](https://s2.loli.net/2024/02/24/9iB5pnROVIZHWza.png)

## 使用SSH连接到重启之后的盒子并写入EMMC

盒子成功启动后会自动从DHCP服务器获取IP地址，进入路由器后台查看

![Snipaste_2024-02-24_18-09-44.png](https://s2.loli.net/2024/02/24/IOmpHJrMWlknZ9N.png)

使用SSH连接工具连接后默认的用户/密码是 root/1234 跟随提示设置普通用户和更改密码

### 写入EMMC

这里直接照搬项目README

登录 Armbian 系统 (默认用户: root, 默认密码: 1234) → 输入命令：

```shell
armbian-install
```

| 可选参数 | 默认值 | 选项   | 说明                                                        |
| -------- | ------ | ------ | ----------------------------------------------------------- |
| -m       | no     | yes/no | 使用 Mainline u-boot                                        |
| -a       | yes    | yes/no | 使用 [ampart](https://github.com/7Ji/ampart) 分区表调整工具 |
| -l       | no     | yes/no | List. 显示全部设备列表                                      |

举例: `armbian-install -m yes -a no`

**注意**！！！有些型号写入EMMC可能会失败 需要反编译dtb文件 修改emmc频率

几个参考的issue 

[https://github.com/ophub/amlogic-s9xxx-armbian/issues/1603#issuecomment-1675634318](https://github.com/ophub/amlogic-s9xxx-armbian/issues/1603#issuecomment-1675634318)

[https://github.com/ophub/amlogic-s9xxx-armbian/issues/2007#issue-2074390845](https://github.com/ophub/amlogic-s9xxx-armbian/issues/2007#issue-2074390845)

## armbian-update命令升级内核之后成砖头的解决办法

盒子输入armbian之后默认启动顺序是 tf卡→u盘→emmc

将任意烧录好armbian系统的u盘插上 等待启动后执行命令`armbian-update -s`用来恢复系统内核

参见[https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/documents/README.cn.md#10-%E6%9B%B4%E6%96%B0-armbian-%E5%86%85%E6%A0%B8](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/documents/README.cn.md#10-%E6%9B%B4%E6%96%B0-armbian-%E5%86%85%E6%A0%B8)
