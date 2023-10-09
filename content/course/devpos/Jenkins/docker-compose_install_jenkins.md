---
title: docker-compose 安装
date: '2022-10-21'
type: book
weight: 20
---



touch docker-compose.yml
# 写入下面类容
```YAML
version: "3.3"
services:
  jenkins:
    image: jenkins/jenkins:lts
    restart: unless-stopped
    privileged: true
    user: root
    ports:
      - 8080:8080
    container_name: jenkins
    volumes:
      - ~/jenkins:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/local/bin/docker:/usr/local/bin/docker
```

sudo docker-compose up -d
浏览器访问
ip:8080  即可访问

