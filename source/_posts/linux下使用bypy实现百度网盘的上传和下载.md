---
title: linux下使用bypy实现百度网盘的上传和下载
tags:
  - [linux]
  - [bypy]
categories:
  - gallery
date: 2023-11-22 17:29:29
---

首先需要系统中安装有python的环境，然后使用pip安装`bypy`
```shell
pip install bypy
```
安装完成之后，在终端中输入
```shell
bypy info
```
这时候命令会返回一个网址，把这个网址复制出来到浏览器中打开，然后按照要求登录百度网盘进行授权，授权之后会在网页上显示一个token，将token复制回终端中即可授权bypy。  
授权之后，百度网盘中会出现一个bypy文件夹，具体路径如下图所示:  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311221735670.png)  
之后所有的文件或者文件夹都在这里进行中转。  
然后就可以回到linux终端下进行文件上传和下载操作了。  
```shell
bypy list
#显示百度网盘bypy目录下的所有文件

bypy downfile xxx
#下载xxx文件到本地

bypy downdir /xxx
#下载xxx文件夹到本地

bypy upload xxx
#上传xxx文件到百度网盘bypy文件夹下

bypy -h
#查看更多命令和使用帮助
```
