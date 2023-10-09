---
title: phpipam
date: '2023-05-04'
type: book
weight: 30
highlight: true
tags:
  - current
---


# 安装phpIPAM监控内网所有ip地址

[官方文档](https://hub.docker.com/r/phpipam/phpipam-www)

[参考文档](https://zhuanlan.zhihu.com/p/573011083)

vim docker-compose.yml 

```YAML
version: '3'

services:
  phpipam-web:
    privileged: true
    image: phpipam/phpipam-www:latest
    ports:
      - "64080:80"
    environment:
      - TZ=Asia/Shanghai
      - IPAM_DATABASE_HOST=phpipam-mariadb
      - IPAM_DATABASE_PASS=Cisco
      - IPAM_DATABASE_WEBHOST=%
    restart: unless-stopped
    volumes:
      - phpipam-logo:/phpipam/css/images/logo
    depends_on:
      - phpipam-mariadb

  phpipam-cron:
    privileged: true 
    image: phpipam/phpipam-cron:latest
    environment:
      - TZ=Asia/Shanghai
      - IPAM_DATABASE_HOST=phpipam-mariadb
      - IPAM_DATABASE_PASS=Cisco
      - SCAN_INTERVAL=1h
    restart: unless-stopped
    depends_on:
      - phpipam-mariadb

  phpipam-mariadb:
    privileged: true 
    image: mariadb:latest
    ports:
      - "3306:3306" 
    environment:
      - MYSQL_ROOT_PASSWORD=Cisco
    restart: unless-stopped
    command:
      - mysqld
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    volumes:
      - phpipam-db-data:/var/lib/mysql

volumes:
  phpipam-db-data:
  phpipam-logo:
```
docker-compose up -d && docker-compose logs -f

