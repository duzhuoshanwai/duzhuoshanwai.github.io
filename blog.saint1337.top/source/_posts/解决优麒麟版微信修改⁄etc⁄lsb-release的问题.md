---
title: 优麒麟版微信修改/etc/lsb-release后识别错误问题
date: 2023-11-06 11:55:06
---

## 问题发现

使用 [tabby](https://tabby.sh/) 官方提供的 Debian repo 时 发现识别系统为 Kylin 导致无法使用脚本添加 repo

查看脚本信息发现检测的文件为 `/etc/lsb-release` 

![Screenshot_20231106_094050.png](https://s2.loli.net/2023/11/06/jFpxWTIfl6gtmQ5.png)

## 解决办法

一番检索后找到了这个帖子 [https://v2ex.com/t/906470](https://v2ex.com/t/906470) 发现应该是优麒麟版微信在 `/etc` 里面拉屎 ~~张小龙你吗死了~~

添加 repo 的脚本内容先检测是否存在 `/etc/lsb-release` 再查看二进制文件 `lsb_release` 所以识别到的系统是 `Kylin`

![Screenshot_20231106_104227.png](https://s2.loli.net/2023/11/06/o4MYQ3CG6WXNzV5.png)

查看原版 Debian 使用的的是 `lsb_release` 所以我们可以直接删除 `/etc/lsb-release` 这个文件 或者手动修改 `/etc/lsb-release` 

``````
DISTRIB_ID=Debian
DISTRIB_RELEASE=n/a
DISTRIB_CODENAME=trixie
DISTRIB_DESCRIPTION="Debian GNU/Linux trixie/sid"
``````

现在重新运行脚本

![Screenshot_20231106_095703.png](https://s2.loli.net/2023/11/06/mW4Ol9uNDLJdtzn.png)

成功！
