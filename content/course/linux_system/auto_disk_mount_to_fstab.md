---
title: Linux 磁盘自动挂载
date: '2023-10-12'
type: book
weight: 20
---

## 自动分区，自动格式化，自动添加永久磁盘挂载

> 创建一个auto_mount_disk_to_fstab.sh 文件

vim auto_mount_disk_to_fstab.sh

> 写入一下类容

```bash
#!/bin/bash

# 创建挂在目录
mkdir -pv /data

# 这里是的sdb磁盘盘符要根据你环境真是的名称填写

#  **注意从 <<EOF 的内容到 EOF 的内容不能删除 否则无法分区成功**

fdisk /dev/sdb <<EOF
n
p



w

EOF

# 格式化磁盘 这里的sdb1也要根据你环境真实的名称填写
mkfs.ext4 /dev/sdb1
# 把磁盘的uuid写入到/etc/fstab中
echo $(blkid /dev/sdb1 | awk  '{print $2}')  /data ext4 defaults 0 0 | tee -a /etc/fstab


# 测试挂在是否成功
mount -av
```
