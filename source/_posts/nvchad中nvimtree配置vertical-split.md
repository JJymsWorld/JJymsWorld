---
title: nvchad中nvimtree配置vertical-split
tags:
  - [neovim]
  - [nvchad]
  - [nvimtree]
categories:
  - gallery
date: 2024-01-21 22:45:44
---
nvimtree中默认的vertical-split的快捷键是`<C-v>`而在nvchad中这个快捷键与粘贴功能冲突，并且nvchad的作者并没有解决这个问题，所以这里记录下自定义修改nvimtree快捷键map的过程。  
首先进入到配置neovim的文件夹，进入`nvim\lua\plugins\configs`找到`nvimtree.lua`  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202401212250013.png)  
图中红框标注的是新增的代码，箭头指向的地方改成快捷键，以及对应的api，api可以在nvimtree的官方github仓库中进行查看。

