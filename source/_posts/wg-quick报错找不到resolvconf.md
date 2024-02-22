---
title: '解决 wg-quick 报错resolvconf: command not found'
date: 2023-11-28 17:55:06
---
## 当前环境
* `Linux armbian 5.15.139-ophub #1 SMP PREEMPT Mon Nov 20 22:33:18 EST 2023 aarch64 aarch64 aarch64 GNU/Linux                                ` 
* 使用的软件源 `Ubuntu Ports 23.04 luner`

## 问题详情

``````shell
root@armbian:/etc/wireguard# sudo wg-quick up wg0
[#] ip link add wg0 type wireguard
[#] wg setconf wg0 /dev/fd/63
[#] ip -4 address add 10.0.8.9/24 dev wg0
[#] ip link set mtu 1420 up dev wg0
[#] resolvconf -a wg0 -m 0 -x
/usr/bin/wg-quick: line 32: resolvconf: command not found
[#] ip link delete dev wg0
``````



## 解决步骤

问题是由无法调用 `resolvconf`命令产生的 我们通过直接安装来解决 **不同发行版的软件包可能不同**

```shell
root@armbian:/etc/wireguard# sudo apt install openresolv
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package openresolv is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'openresolv' has no installation candidate
root@armbian:/etc/wireguard# resolvconf
Command 'resolvconf' not found, but can be installed with:
apt install systemd-resolved
root@armbian:/etc/wireguard# apt install systemd-resolved

...succeed

root@armbian:/etc/wireguard# systemctl start wg-quick@wg0
```

