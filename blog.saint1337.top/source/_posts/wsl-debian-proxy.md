---
title: wsl2+宿主机v2rayN 实现命令行proxy
date: 2023-04-13 15:09:06
tags:
---

## 环境

- v2rayn version 5.39
- wsl2 + Debian

## v2rayN 设置局域网 socks5 / http proxy

- core:基础设置中勾选允许来自局域网的连接和开启 Mux 多路复用 ( 推荐设置认证密码 )
- 在选择节点窗口下方查看共享给局域网的端口

## 查看宿主机的 ip 和 wsl 的 ip

- 获取宿主机 ip ```cat /etc/resolv.conf```
  
- 同时添加代理到 `/etc/environment` `~/.profile`
  
  也可以添加到 `~/.bashrc` `~/.zshrc`

```
### http代理

export windows_host=`cat /etc/resolv.conf|grep nameserver|awk '{print $2}'`
export ALL_PROXY=socks5://{username}:{passwd}@$windows_host:{port}
export HTTP_PROXY=$ALL_PROXY
export http_proxy=$ALL_PROXY
export HTTPS_PROXY=$ALL_PROXY
export https_proxy=$ALL_PROXY
export no_proxy=127.0.0.1,.devops.com,localhost,local,.local,172.28.0.0/16

if [ "`git config --global --get proxy.https`" != "socks5://{username}:{passwd}@$windows_host:{port}" ]; then
            git config --global proxy.https socks5://{username}:{passwd}@$windows_host:{port}
fi

```

- ```source /etc/environment&&source ~/.profile```

## 测试连接

- ```wget google.com```

- ```curl google.com```

- 有返回值说明正常

  