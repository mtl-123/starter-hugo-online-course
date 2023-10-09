---
title: dpkg命令的使用
date: '2022-10-17'
type: book
weight: 20
---


## 安装deb包
```bash
sudo dpkg -i *.deb
```
## 卸载使用dpkg 安装的deb包
```bash
# 先列出dpkg 安装的软件
sudo dpkg -l
# 再使用
sudo dpkg -r  软件名称

```