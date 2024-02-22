---
title: ClashX Pro + UnblockNeteaseMusic
date: 2023-02-22 21:31:06
tags:
---


## 准备

- [UnblockNeteaseMusic/server](https://github.com/UnblockNeteaseMusic/server)

- [ClashX Pro](https://install.appcenter.ms/users/clashx/apps/clashx-pro/distribution_groups/public)

- [网易云音乐 2.3.12_956](https://d1.music.126.net/dmusic/NeteaseMusic_2.3.12_956_web.dmg)

- 安装证书 [ca.crt](https://raw.githubusercontent.com/nondanee/UnblockNeteaseMusic/master/ca.crt)

## ClashX Pro 设置并添加规则

- 开启 Enhanced mode

  - Enhanced mode Config → DNS mode → Mapping

- 编辑 config.yaml

  ```yaml
  proxies:
    - { name: NeteaseMusicUnlock, type: http, server: 192.168.1.2, port: 8080 }
  ```

  ```yaml
  proxy-groups:
    - name: NeteaseMusic
  type: select
  proxies:
    - DIRECT
    - NeteaseMusicUnlock
  ```

  ```yaml
  rules:
    - DOMAIN-SUFFIX,music.163.com,NeteaseMusic
    - DOMAIN-SUFFIX,music.126.net,NeteaseMusic
    - DOMAIN-SUFFIX,163yun.com,NeteaseMusic
    - DOMAIN-SUFFIX,api.iplay.163.com,NeteaseMusic
    - DOMAIN-SUFFIX,apm.music.163.com,NeteaseMusic
    - DOMAIN-SUFFIX,apm3.music.163.com,NeteaseMusic
    - DOMAIN-SUFFIX,interface.music.163.com,NeteaseMusic
    - DOMAIN-SUFFIX,interface3.music.163.com,NeteaseMusic
    - DOMAIN-SUFFIX,mam.netease.com,NeteaseMusic
    - DOMAIN-SUFFIX,hz.netease.com,NeteaseMusic
    - IP-CIDR,39.105.63.80/32,NeteaseMusic
    - IP-CIDR,45.254.48.1/32,NeteaseMusic
    - IP-CIDR,47.100.127.239/32,NeteaseMusic
    - IP-CIDR,59.111.21.14/31,NeteaseMusic
    - IP-CIDR,59.111.179.214/32,NeteaseMusic
    - IP-CIDR,59.111.181.38/32,NeteaseMusic
    - IP-CIDR,59.111.181.60/32,NeteaseMusic
    - IP-CIDR,59.111.160.195/32,NeteaseMusic
    - IP-CIDR,59.111.160.197/32,NeteaseMusic
    - IP-CIDR,59.111.181.35/32,NeteaseMusic
    - IP-CIDR,59.111.238.29/32,NeteaseMusic
    - IP-CIDR,101.71.154.241/32,NeteaseMusic
    - IP-CIDR,103.126.92.132/32,NeteaseMusic
    - IP-CIDR,103.126.92.133/32,NeteaseMusic
    - IP-CIDR,112.13.119.17/32,NeteaseMusic
    - IP-CIDR,112.13.122.1/32,NeteaseMusic
    - IP-CIDR,115.236.118.33/32,NeteaseMusic
    - IP-CIDR,115.236.121.1/32,NeteaseMusic
    - IP-CIDR,118.24.63.156/32,NeteaseMusic
    - IP-CIDR,193.112.159.225/32,NeteaseMusic
    - IP-CIDR,223.252.199.66/32,NeteaseMusic
    - IP-CIDR,223.252.199.67/32,NeteaseMusic
  ```

## Reload Config And Enjoy !
![](https://raw.githubusercontent.com/duzhuoshanwai/Image-Hosting/master/img/202302222256214.png)