---
title: phpMyAdmin + MySQL docker-compose.yaml
date: 2023-09-12 15:09:06
tags:
---

```
version: '3'

networks:
  mysql-phpmyadmin:
    name: mysql-phpmyadmin
    driver: bridge

volumes:
  mysqldata:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: '/home/duzhuo/docker/mysql/data'

services:
  mysql:
    image: mysql
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: database
      MYSQL_USER: duzhuo
      MYSQL_PASSWORD: 123456
    ports:
      - "33060:3306"
    volumes:
      - mysqldata:/var/lib/mysql
    networks:
      # Connect the mysql container to the mysql-phpmyadmin network and set the alias as mysql
      mysql-phpmyadmin:
        aliases:
          - mysql

  phpmyadmin:
    image: phpmyadmin
    container_name: phpmyadmin
    links:
      - mysql
    environment:
      PMA_HOST: mysql
      PMA_PORT: 3306
    ports:
      - 8081:80
    networks:
      # Connect the phpmyadmin container to the mysql-phpmyadmin network and set the alias as phpmyadmin
      mysql-phpmyadmin:
        aliases:
          - phpmyadmin
```