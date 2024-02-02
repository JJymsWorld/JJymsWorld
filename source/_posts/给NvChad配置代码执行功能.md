---
title: 给NvChad配置代码执行功能
tags:
  - [neovim]
categories:
  - gallery
date: 2024-01-27 20:27:26
---
## 前言
因为平时习惯了使用vim进行一些简单代码的编写和一些目录结构比较简单的项目开发，效率也非常高，但是因为要经常在本地和服务器进行切换，懒得进行配置了，之前是整理了一套spacevim的配置流程，每换个设备就配置一下也非常快，但是最近越发觉得spacevim过于臃肿了，很多功能用不到，启动速度也比较慢，甚至快不如vscode了，于是切换到了更加轻量化的NvChad，但是NvChad没有spacevim那样的直接`spec l r`就直接运行代码的功能，遂打算自己配置一个，这里使用 jaq-nvim[https://github.com/is0n/jaq-nvim] 来进行对neovim配置代码执行功能。
## 步骤
### 安装插件
首先，NvChad是使用lazyvim进行插件管理的，我们先去进行插件的安装，首先进入到NvChad的插件配置文件里面，这里我是在`nvim\lua\custom\plugins.lua`中进行配置  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202401272035330.png)  
在这里进行插件的安装。
### 插件配置
接下来到`configs\override.lua`中对插件进行配置 ![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202401312146529.png) 
这里我是直接参考的官方的文档进行配置的，可以去作者的GitHub上进行查看，当然作者没有给c和cpp的执行指令，这里是我自己进行配置的，可以参考，具体的作用是创建`build`文件夹，然后将编译出的可执行文件放入build文件夹中，避免污染当前目录，然后执行编译出的可执行文件。注释的部分是在当前目录下生成可执行文件，然后执行可执行文件之后将可执行文件删除，这里加了timeout指令，是cmd下的延时指令，至于为什么加这个指令是因为如果直接del可执行文件，它会提示程序正在执行中无法删除，具体原因我也不知道为什么或者怎么解决，所以加了延迟1s，这样就不会提示无法删除了。  
### 启用插件  
到`nvim\lua\plugins\init.lua`中启用插件  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202401272047201.png)  
## 测试
经过上面的配置，已经可以直接进行代码执行，直接执行`:Jaq`指令即可  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202401272054241.png)  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202401272121002.png)