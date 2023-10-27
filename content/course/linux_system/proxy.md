---
title: Linux 代理服务
date: '2023-10-06'
type: book
weight: 20
---

<!--more-->



{{< icon name="clock" pack="fas" >}} 每周1-2小时，持续8周

## SwitchyOmega 浏览器代理插件配置教程

[google浏览器下载地址](https://chromewebstore.google.com/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)

[github下载地址](https://github.com/FelisCatus/SwitchyOmega/releases)

{{< youtube v_v5LhEtX0Y >}}


## 系统代理
[github Windows 版本下载 ](https://github.com/shunf4/proxychains-windows)

[linux 配置 ](https://boxmaking.github.io/linux/proxychains4.html)
[windows  配置 ](https://zsxwz.com/2022/10/28/proxychains%e5%91%bd%e4%bb%a4%e8%a1%8c%e4%bb%a3%e7%90%86%e8%bd%ac%e5%8f%91%e5%b7%a5%e5%85%b7%ef%bc%8cwindows%e7%ae%80%e5%8d%95%e4%bd%bf%e7%94%a8/)



{{< youtube h_lODOsZW_c >}}


## 科学上网代理软件
[github 包含 windows linux 软件包 ](https://github.com/Fndroid/clash_for_windows_pkg/releases)



## Linux 终端运行clash代理

所需软件如下：
- 1、[clash-linux-amd64](https://github.com/Dreamacro/clash/releases/download/v1.18.0/clash-linux-amd64-v1.18.0.gz)
- 2、[Country.mmdb](https://cdn.jsdelivr.net/gh/Dreamacro/maxmind-geoip@release/Country.mmdb)
- 3、config.yaml 这个文件是手动创建的 如 touch config.yaml  （需在已经运行的clash软件打开图形化界面->配置->单机节点编辑复制节点内容再粘贴再创建的config.yaml文件中保存）
- 4、proxychains

部署：
```bash
mkdir clash-server
cd clash-server
# 下载
wget https://github.com/Dreamacro/clash/releases/download/v1.18.0/clash-linux-amd64-v1.18.0.gz
# 需要科学上网才能下载
wget https://cdn.jsdelivr.net/gh/Dreamacro/maxmind-geoip@release/Country.mmdb
解压
gzip -d  clash-linux-amd64-v1.18.0.gz
mv clash-linux-amd64-v1.18.0 clash
chmod +x clash
# 创建config.yaml文件
touch config.yaml
# 在可用的clash gui 的配置-节点-编辑-复制内容写入到config.yaml文件中并保存
# 启动，看到一下信息就算启动成功了！
./clash -f config.yaml -d .
hexo@hexo:~$ ./clash -f config.yaml -d .
INFO[0000] Can't find MMDB, start download
INFO[0026] Start initial compatible provider 故障转移
INFO[0026] Start initial compatible provider 自动选择
INFO[0026] Start initial compatible provider 三分机场
INFO[0026] RESTful API listening at: 127.0.0.1:9090
INFO[0026] inbound mixed://:7890 create success.
INFO[0596] [TCP] 127.0.0.1:55102 --> cdn.jsdelivr.net:443 match Match() using 三分机场[🇺🇸美国高速接入点-04]

# 安装proxychains代理
apt install -y proxychains*
# 配置，修改最后一行的
sudo vim /etc/proxychains4.conf
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 7890  #修改成这样即可

# 测试 curl www.google.com 如下输出很多信息就算科学上网成功了

hexo@hexo:~$ proxychains4 curl www.google.com
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] Strict chain  ...  127.0.0.1:7890  ...  www.google.com:80  ...  OK
<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="en"><head><meta content="Search the world's information, including webpages, images, videos and more. Google has many special features to help you find exactly what you're looking for." name="description"><meta content="noodp" name="robots"><meta content="text/html; charset=UTF-8" http-equiv="Content-Type"><meta content="/images/branding/googleg/1x/googleg_standard_color_128dp.png" itemprop="image"><title>Google</title><script nonce="eoxUgRDLm
```





