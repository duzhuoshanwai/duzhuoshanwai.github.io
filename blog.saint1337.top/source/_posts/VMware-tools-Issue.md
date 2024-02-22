title: VMware-tools 无法安装问题解决
date: 2023-2-6 09:29:00
tags: 
- VMware
---


## 问题描述
- WMware版本 WMware Workstation PRO 16.2.3
- 安装镜像版本:Windows7 x64
- 自动安装的VMware Tools 版本为11.3.5 
  出现提示Windows无法验证此驱动程序软件发布者
  导致出错无法安装
### 图示如下
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941347.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941335.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941323.png)
## 解决办法
- 安装 VMware Tools 11.0.0
- 挂载iso镜像到虚拟机
- 运行setup64.exe
- 安装成功后重启
### 图示如下
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941309.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941297.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941281.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941271.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941258.png)
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302060941246.png)

## 相关下载链接与分流
[VM-tools](http://softwareupdate.vmware.com/cds/vmw-desktop/ws/15.5.0/14665864/windows/packages/tools-windows.tar)
[VM-tools OneDrive分流](https://dlink.host/sharepoint/aHR0cHM6Ly9zYWludDEzMzctbXkuc2hhcmVwb2ludC5jb20vOnU6L2cvcGVyc29uYWwvZHV6aHVvX3NhaW50MTMzN19vbm1pY3Jvc29mdF9jb20vRWFsZ0UyeWMtZUpEdTdaZW1ZWlllX1VCakF4cFNzd2d1NlNqNDU1dzZqdVFLZz9lPXk3MGhETQ.iso)

[提示升级Win7版本到sp1 相关补丁](https://www.catalog.update.microsoft.com/search.aspx?q=kb4474419)

