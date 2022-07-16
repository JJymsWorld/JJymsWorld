---
title: archlinux下kde+xorg设置qtcreator界面缩放
tags:
  - [archlinux]
categories:
  - gallery
date: 2022-07-16 16:58:02
---

在/usr/share/applications中找到qtcretor的desktop文件，使用vim编辑此文件，在Exec=后方加入`QT_SCREEN_SCALE_FACTORS=1`即可。
