---
title: System has not been booted with systemd as init system (PID 1). 
date: 2023-04-20 15:09:06
tags:
---

## 升级 wsl 
  - **注意开启 windows update**
  - `wsl --update`
  - `wsl --version`
    ```
    WSL version: 1.1.3.0
    Kernel version: 5.15.90.1
    WSLg version: 1.0.49
    MSRDC version: 1.2.3770
    Direct3D version: 1.608.2-61064218
    DXCore version: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
    Windows version: 10.0.22621.525
    ```
## 开启 Systemd support
- `sudo nano /etc/wsl.conf`
  
    ```
    [boot]
    systemd=true
    ```

- `^O` + `^X`
- `wsl --shutdown`

## Enjoy !