---
title: pandora+docker 私有化部署ChatGPT
date: 2023-04-06 15:09:06
tags:
---

## 环境

## 更新环境

### Debian11 + Docker

- Linux duzhuo 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64 GNU/Linux
- Docker Compose version v2.17.2
- Docker version 23.0.2, build 569dd73

- `apt-get update -y`
  
- ```apt-get install apt-transport-https software-properties-common ca-certificates curl gnupg lsb-release -y```

- ```sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin```

### 需要的 Docker 镜像

- pengzhile/pandora

  `docker pull pengzhile/pandora`

- nginx

  `docker pull nginx`

## 配置文件

### tree

``` 
root@duzhuo:~/.pandora# tree
.
├── data
│   └── access_token.dat
├── docker-compose-pandora.yaml
├── docker-compose.yaml.bak
└── nginx
    ├── certs
    │   ├── cert.pem
    │   └── key.pem
    ├── htpasswd
    └── nginx.conf

3 directories, 7 files
root@duzhuo:~/.pandora# 
```

### docker-compose.yaml

```
version: '3.8'

# 定义一个名为 "pandora" 的 Docker 网络
networks:
  pandora:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.0.0/24

# 定义两个 Docker 服务: "Nginx-Reverse-proxy" 和 "pandora"
services:
  # Nginx-Reverse-proxy 服务
  Nginx-Reverse-proxy:
    # 使用 nginx Docker 镜像
    image: nginx
    # 将端口 80 和 443 映射到宿主机
    ports:
      - 80:80
      - 443:443
    # 除非停止，否则重新启动容器
    restart: unless-stopped
    # 挂载多个卷以进行配置文件和 SSL 证书
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/certs:/etc/nginx/certs:ro
      - ./nginx/htpasswd:/etc/nginx/htpasswd:ro
      - ./nginx/html:/usr/share/nginx/html:ro
    # 将容器连接到 "pandora" 网络并分配一个 IPv4 地址
    networks:
      pandora:
        ipv4_address: 192.168.0.4

  # pandora 服务
  pandora:
    # 使用 pengzhile/pandora Docker 镜像
    image: pengzhile/pandora
    # 将容器命名为 "pandoraGPT"
    container_name: pandoraGPT
    # 将容器连接到 "pandora" 网络并分配一个 IPv4 地址
    networks:
      pandora:
        ipv4_address: 192.168.0.2
    # 将 "data" 目录作为容器内的卷挂载
    volumes:
      - ./data:/data
    # 除非停止，否则重新启动容器
    restart: unless-stopped
    # 设置多个环境变量
    environment:
      # 将 PANDORA_SERVER 环境变量设置为监听所有可用的网络接口和端口 18888
      PANDORA_SERVER: 0.0.0.0:18888
      # 将 OPENAI_EMAIL 环境变量设置为指定的电子邮件地址
      OPENAI_EMAIL: {YourAccount}
      # 将 OPENAI_PASSWORD 环境变量设置为指定的密码
      OPENAI_PASSWORD: {YourPassword}
      # 建议替换 OPENAI_EMAIL 和 OPENAI_PASSWORD
      # PANDORA_ACCESS_TOKEN: {YourAccessToken}
```

#### ACCESS TOKEN 获取

[https://chat.gateway.do/auth](https://chat.gateway.do/auth)


### Nginx.conf

```
user  nginx;  # 设置运行Nginx的用户为nginx

worker_processes  1;  # 设置工作进程数为1

events {

    worker_connections  128;  # 每个worker进程最多允许的连接数

}

http {

    server {
        listen 80;  # 监听80端口，处理http请求

        server_name chat.duzhuo.eu.org;  # 域名

        return 301 https://$host$request_uri;  # 将所有http请求重定向到https
    }

    server {
        listen 443 ssl http2;  # 监听443端口，处理https请求，启用http2协议
        server_name chat.duzhuo.eu.org;  # 域名

        ssl_certificate /etc/nginx/certs/cert.pem;  # SSL证书路径
        ssl_certificate_key /etc/nginx/certs/key.pem;  # SSL证书密钥路径

        auth_basic "Restricted";  # 开启基本认证
        auth_basic_user_file /etc/nginx/htpasswd;  # 用户名密码文件路径

        location / {
            proxy_pass http://192.168.0.2:18888;  # 将请求转发给目标服务器
            proxy_set_header Host $host;  # 设置Host请求头
            proxy_set_header X-Real-IP $remote_addr;  # 设置X-Real-IP请求头
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  # 设置X-Forwarded-For请求头
        }
    }
}
```

#### auth_basic密码设置

```apt-get update```

```apt-get install apache2-utils```

初次创建htpasswd文件 并添加一个叫user的用户

```htpasswd -c /path/to/htpasswd user```

向htpasswd文件中添加一个叫anotheruser的用户

```htpasswd /path/to/htpasswd anotheruser```

## 获取一个SSL证书

### 通过acme.sh

[https://acme.sh](https://acme.sh)

### 通过Cloudflare

[https://cloudflare.com](https://cloudflare.com)


证书放置目录 参考 [tree](#tree "tree")


## 启动和停止服务

```
root@duzhuo:~/.pandora# docker-compose -f docker-compose-pandora.yaml up -d
[+] Running 3/3
 ✔ Network pandora_pandora                  Created                                                                                                    0.2s 
 ✔ Container pandoraGPT                     Started                                                                                                    3.1s 
 ✔ Container pandora-Nginx-Reverse-proxy-1  Started                                                                                                    3.1s 
root@duzhuo:~/.pandora#
```

```
root@duzhuo:~/.pandora# docker-compose -f docker-compose-pandora.yaml down
[+] Running 3/3
 ✔ Container pandoraGPT                     Removed                                                                                                   12.4s 
 ✔ Container pandora-Nginx-Reverse-proxy-1  Removed                                                                                                    3.0s 
 ✔ Network pandora_pandora                  Removed                                                                                                    0.4s 
root@duzhuo:~/.pandora#  
```

## Open and play !
![pic](https://s2.loli.net/2023/04/06/Myb6QP8z5EC1G2n.png)