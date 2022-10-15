---
title: hugo项目框架说明
date: 2022-10-15
---


hugo博客结构目录说明

<!--more-->


# 博客项目结构

```bash
├─.github
├─assets  		# 资产
│  ├─media		# 媒体
│  │  └─icons	# 图标
│  └─scss
├─config		# 配置
│  └─_default
├─content		# 内容
│  ├─admin
│  ├─authors	# 作者
│  │  ├─admin
│  │  └─吳恩達
│  ├─contact	# 联系
│  ├─course
│  │  ├─data-science	# 数据科学
│  │  └─robotics
│  ├─home				# 主页
│  └─post				# 博客编写位置
│      ├─01 # 这是创建的文件夹名称，在该文件夹下面可新建一个xx.md的markdown文件进行编写博客文章
│      └─02
├─data
│  ├─fonts				# 字体
│  └─themes				# 主题
├─resources				# 资源
│  └─_gen
│      ├─assets			# 资产
│      │  └─scss
│      │      └─scss
│      └─images			# 图像
│          ├─authors	# 作者
│          │  ├─admin
│          │  └─吳恩達
│          ├─course		# 课程
│          │  ├─data-science	# 数据科学
│          │  └─robotics		# robotics
│          └─media				# 媒体
└─static						# 静态网页
    └─uploads					# 上传
```




