---
title: Network 网络监控
date: '2023-07-27'
type: book
weight: 20
---

## 网络监控
### Windows 命令

`tracert www.baidu.com`

![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/b03be561-a4ac-443e-b695-6d778db8c6d2)
`nslookup   www.baidu.com`

## 查看指定端口号

`netstat -ano  | findstr 10050`

### Linux 命令
`traceroute www.baidu.com`

## nmap 端口扫描
Windows使用
扫描存活主机
`nmap -n -Pn -T5 -sS 192.168.1.0/23`

![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/d24dabdd-600f-48c8-ad56-ffd2339bff5a)
