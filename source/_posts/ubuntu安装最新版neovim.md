---
title: ubuntu安装最新版neovim
tags:
  - [neovim]
categories:
  - gallery
date: 2023-11-08 14:51:04
---

首先需要卸载旧版，如果没装过不需要卸载  
```bash
sudo remove neovim
```
然后从github上面下载最新的neovim压缩包
```bash
sudo wget https://github.com/neovim/neovim/releases/download/nightly/nvim-linux64.tar.gz
```
然后解压，并移动
```bash
tar -xzvf nvim-linux64.tar.gz
mv nvim-linux64 /usr/local/nvim
```
创建软链接
```bash
sudo ln -s /usr/local/nvim/bin/nvim /usr/bin/nvim
```
完成！然后可以输入nvim -v查看版本信息