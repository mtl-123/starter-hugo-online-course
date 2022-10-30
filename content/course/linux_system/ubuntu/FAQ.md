---
title: ubuntu系统常见文件
date: '2022-10-31'
type: book
weight: 20
---

# ubuntu18.06

## 无法调节亮度

ubuntu18.04系统无法通过调节右上角的小太阳调节屏幕亮度

```bash
sudo add-apt-repository ppa:apandada1/brightness-controller

sudo apt update
sudo apt install brightness-controller
# 打开这个工具即可调试亮度了
brightness-controller
```
