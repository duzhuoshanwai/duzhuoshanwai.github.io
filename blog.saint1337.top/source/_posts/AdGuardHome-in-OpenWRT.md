---
title: '在OpenWRT上使用AdGuard Home的DNS服务代替dnsmasq'
date: 2024-02-02 10:52:35
tags:
---

## 当前环境

![](https://cdn.jsdelivr.net/gh/duzhuoshanwai/Image-Hosting@master/img/Screenshot_20240202_112818.png)

## Step1 从仓库下载二进制文件并解压

``````bash
cd /usr/bin
## 没有curl可以使用opkg安装或者用wget替代
curl -O https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.107.42/AdGuardHome_linux_arm64.tar.gz
tar -zxvf ./AdGuardHome_linux_arm64.tar.gz
rm -rf ./AdGuardHome_linux_arm64.tar.gz
cd ./AdGuardHome
``````

## Step2 启动AdGuardHome并进入后台

``````bash
./AdGuardHome
``````

出现类似下面的提示就代表AdGuardHome已经启动

![](https://cdn.jsdelivr.net/gh/duzhuoshanwai/Image-Hosting@master/img/Screenshot_20240202_152653.png)

这样大概就是成功了，现在我们的AdGuardHome还不能开机运行，需要配置守护进程。

## Step3 配置开机启动

``````
cd /usr/bin/AdGuardHome
./AdGuardHome -s install
``````

这样procd脚本就会被写入到/etc/init.d文件夹里面 

``````bash
root@OpenWrt:/usr/bin/AdGuardHome# cat /etc/init.d/AdGuardHome 
#!/bin/sh /etc/rc.common

USE_PROCD=1

START=95
STOP=01

cmd="/usr/bin/AdGuardHome/AdGuardHome "-s" "run""
name="AdGuardHome"
pid_file="/var/run/${name}.pid"

start_service() {
    echo "Starting ${name}"

    procd_open_instance
    procd_set_param command ${cmd}
    procd_set_param respawn             # respawn automatically if something died
    procd_set_param stdout 1            # forward stdout of the command to logd
    procd_set_param stderr 1            # same for stderr
    procd_set_param pidfile ${pid_file} # write a pid file on instance start and remove it on stop

    procd_close_instance
    echo "${name} has been started"
}

stop_service() {
    echo "Stopping ${name}"
}

EXTRA_COMMANDS="status"
EXTRA_HELP="        status  Print the service status"

get_pid() {
    cat "${pid_file}"
}

is_running() {
    [ -f "${pid_file}" ] && ps | grep -v grep | grep $(get_pid) >/dev/null 2>&1
}

status() {
    if is_running; then
        echo "Running"
    else
        echo "Stopped"
        exit 1
    fi
}
root@OpenWrt:/usr/bin/AdGuardHome# 
``````

## Step4 修改53端口的服务由AdGuardHome提供

在 `/etc/config/dhcp`中找到`option port '53'` 并将53改为0

```bash
service dnsmasq restart
## 这样我们就关闭了dnsmasq提供的dns服务
```

![](https://cdn.jsdelivr.net/gh/duzhuoshanwai/Image-Hosting@master/img/Screenshot_20240202_160901.png)

现在我们修改AdGuardHome提供dns服务的端口

打开 /usr/bin/AdGuardHome/AdGuardHome.yaml （我们之前解压二进制文件的目录） 并替换端口为53

```bash
service AdGuardHome restart
## 重新启动后查看端口情况
netstat -tunlp | grep 53
```



![](https://cdn.jsdelivr.net/gh/duzhuoshanwai/Image-Hosting@master/img/Screenshot_20240202_161643.png)

##　附

[https://dns.icoa.cn/](https://dns.icoa.cn/) 可用的上游dns列表

