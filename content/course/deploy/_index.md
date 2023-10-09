---
title: 🤖 运维开发
linkTitle: Home
summary: 运维开发技术记录
date: '2022-10-16'
type: book
tags:
  - current
---


{{< figure src="deploy.jpg" >}}

{{< toc hide_on="x1" >}}

## hugo 搭建
ubuntu安装 hugo
```bash
snap install hugo --channel=extended

hugo --gc --minify

hugo server -D

## 在 Github Pages 上面部署是须在config.toml 中添加如下
baseurl = "//example.com/blog/"
relativeURLs = true
canonifyURLs = true
```
