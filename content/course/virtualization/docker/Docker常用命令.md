---
title: Docker 常用命令
date: '2022-10-16'
type: book
weight: 40
math: true
tags:
  - Statistics
---

{{< toc hide_on="x1" >}}

# 批量打包镜像
```bash
# 使用 grep搜索名称为 k8s.gcr.io的镜像名称并使用xargs命令传参给docker save -o 并打包    
docker images | grep k8s.gcr.io | xargs docker save -o k8s.gcr.io
```
# 批量导入镜像
```bash
# 使用for循环创建一个i变量并使用ls 命令列出 所有*.tar未后缀的文件再使用docker 并引用$i变量导入
for i in `ls *tar`;do  docker load -i $i;done
```
# 查询
`docker info --format {{.CgroupDriver}} `
