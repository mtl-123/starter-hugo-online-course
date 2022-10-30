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
## 安装google输入法

```bash
# 先安装fcitx
sudo apt install fcitx -y
# 安装google拼音
sudo apt install fcitx-googlepinyin
# 配置输入法
桌面左下角的菜单，点击Inout Method 进行设置 -> 选择fcitx -> 选择ok -> 重启电脑
再去点击fcitx配置 -> 添加 googlepinyin 即可
```
