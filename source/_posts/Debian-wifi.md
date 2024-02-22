---
title: GNU/Linux 使用 CLi 连接无线网
date: 2023-10-30 10:55:06
---
## 查看当前无线网卡并启用

```sudo iwconfig``` 查看得到我当前的无线网卡名称是 `wlan0`

![Screenshot_20231030_170101.png](https://s2.loli.net/2023/10/30/9c68AOBhnV1jCRm.png)

```sudo ifconfig wlan0 up``` 启用无线网卡

![Screenshot_20231030_171338_看图王1.png](https://s2.loli.net/2023/10/30/dLewbAYZVifng18.png)

## 扫描可用网络

```sudo iwlist wlan0 scan``` 查找当前可用网络 结果过多的情况下使用  ```grep``` 找出 SSID

![Screenshot_20231030_171426_看图王.png](https://s2.loli.net/2023/10/30/nmWhKYZrxMzvk1V.png)

## 使用 `wpa_supplicant` 连接网络

```sudo wpa_passphrase {SSID} {PASSWD} > sudo /etc/wpa_supplicant/{SSID}.conf```

其中 `/etc/wpa_supplicant/` 是默认存放配置文件的目录

![Screenshot_20231030_172316.png](https://s2.loli.net/2023/10/30/gbX5Z9GdVLuptya.png)

``` sudo wpa_supplicant -i wlan0 -c /etc/wpa_supplicant/{SSID}.conf -B``` 使用我们刚才保存的配置文件和 `wlan0` 网卡连接网络

![Screenshot_20231030_172503_看图王.png](https://s2.loli.net/2023/10/30/d9ZqWeyJ3pzGSAg.png)

**到这一步我们还不能上网** 需要使用 `sudo dhclient wlan0` 获取到 ip 之后才能上网