---
title: 'sudo: unable to resolve host hostname: Name or service not known'
date: 2023-11-09 01:11:17
tags:
---
##  问题复现

使用 `hostnamectl hostname ` 命令更改 hostname 之后使用 `sudo` 后出现报错 ` sudo: unable to resolve host cloudcone: Name or service not known`

## 解决方案

修改 `/etc/hosts` 文件 添加 `127.0.0.1 cloudcone` `::1 cloudcone` cloudcone 是我设置的新 hostname

```
27.0.0.1       localhost
127.0.0.1       cloudcone

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
::1     cloudcone
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

![Screenshot_20231106_125230.png](https://s2.loli.net/2023/11/06/5JPyEbeZHoS3xjR.png)