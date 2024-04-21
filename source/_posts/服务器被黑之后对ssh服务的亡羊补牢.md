---
title: 服务器被黑之后对ssh服务的亡羊补牢
date: 2024-02-28 11:44:42
---

## 起因

前几天突然发现一台服务器掉线了...打开后台一看一整页全部都是Abuse Report，服务器也被suspend。在面板发现服务器被冻结前处于很长一段时间的满负载状态，Ticket内容显示这个ip被举报有扫描行为。

![](https://s2.loli.net/2024/02/28/SyiU2cPsfXGdEK3.png)

通过提交工单请求解冻之后登录，过一段时间发现tor这个用户被盗用并且启动了一个叫`blitz64`的服务。

![](https://s2.loli.net/2024/02/28/M8e71vyWri53Yoa.png)

后面通过删除被修改的`crontab`条目并重启修复，但是**因为我忘了修改密码又被冻结了**。^_^

修复过程因为比较匆忙没有记录，这里附上参考资料。

[My VPS is under attack](https://www.yanxurui.cc/posts/tool/2023-01-02-my-VPS-is-under-attack/)

[Hacked Server](https://dev.to/krowemoh/hacked-server-4kp7)

[记一次ubuntu虚拟机被挖矿木马攻击的过程](https://blog.csdn.net/subfate/article/details/106546646)

[记一次 VPS 被黑掉拉进僵尸网络](https://blog.ataw.top/archives/vps-hacked-and-added-to-botnet?preview-theme=theme-hao)

本文剩余部分用来记录修改密码并添加密钥登录和`fail2ban`的过程。

## 设置ssh密钥登录

