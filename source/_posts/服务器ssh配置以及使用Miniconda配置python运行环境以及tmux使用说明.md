---
title: 服务器ssh配置以及使用Miniconda配置python运行环境以及tmux使用说明
tags:
  - [ssh]
  - [python]
  - [tmux]
categories:
  - gallery
date: 2023-11-05 21:09:38
---

## Vscode ssh远程连接服务器  
首先安装好vscode，去官网下载。   
然后，如果习惯使用中文，去插件里搜索`chinese`，第一个下载安装即可。  
然后去插件安装`Remote-SSH`这个插件，安装后左侧边栏会多出一项。  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311052113119.png)  
然后选择远程资源管理器，鼠标放到ssh上面，点击右侧的设置按钮  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311052116080.png)  
然后选择第一个进去，把config文件中的内容全选复制粘贴替换进去然后保存。  
然后点击刷新按钮，下图所示。再展开SSH即可看到连接列表。
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311052118140.png)
点击箭头或者后面那个都可以，箭头是在本窗口打开，另一个是再打开一个新的窗口连接。
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311052120302.png)
然后选择linux，输入密码即可，可能有些机器需要输入两遍。

## Miniconda环境安装  
首先去[清华原官方](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/?C=M&O=D),找到你要安装的Miniconda版本，一般安装最新的即可，服务器是ubuntu系统，所以看清楚，找到linux x86_64的版本进行复制。  
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311052124345.png)
然后鼠标放上去点击右键然后复制连接。  
然后连接上远程服务器，打开终端，在终端输入命令把安装文件下载到本地:  
```bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py311_23.9.0-0-Linux-x86_64.sh 
# wget后面的地址就是你复制的地址
```
然后输入指令修改文件执行权限:  
```bash
chmod u+x Miniconda3-py311_23.9.0-0-Linux-x86_64.sh
```
最后执行安装文件进行安装:  
```bash
./Miniconda3-py311_23.9.0-0-Linux-x86_64.sh
```
然后根据提示进行安装，许可证那里可以按几次空格跳过，中间让按回车就按回车，让输入yes就输入yes。  
安装完毕后，关掉终端，然后重新打开一下终端。  
测试能够正常执行`conda`指令，即为安装成功。  
然后如果需要创建新的python环境，可以使用指令: 
```bash
conda create -n abc python==3.8
#上面指令中的abc是你要创建的新的环境的名称
#然后后面可以通过
conda activate abc
#来激活abc环境

conda deactivate
#取消激活环境，返回上一个环境
```
conda的具体指令自行去百度学习，学习之后再进行操作，本着节省空间，减少冗余依赖包的原则，先学习好conda的使用，以及conda和pip包管理的区别，再进行使用。  

## tmux使用教程  
简单来说就是，当我们连接上远程服务器之后，我们的会话一旦关闭，那么会话中运行的程序也将会关闭，通常我们进行模型的训练会需要比较长的时间，如果使用原始的方法，我们在服务器上跑程序的同时，也比需保持我们的电脑与服务器的连接，那么更为高级的方法就是我们通过tmux管理会话，当我们一个程序需要跑很久的时候，我们可以不关闭会话，而是detach会话，然后在一段之间之后我们可以重连回会话，并且在detach这段时间，我们的退出并不影响远程服务器上面代码的执行。    

目前服务器上面tmux均已安装好，直接就可以使用，不需要自行安装。  
  
更为详细的使用方法可以参考[阮一峰的教程](https://www.ruanyifeng.com/blog/2019/10/tmux.html)，我这里只进行简单日常的使用的演示。  
  
tmux的使用是在服务器上面的，所以我们首先需要连接上远程终端。  
然后我们可以通过
```bash
tmux new -s abc
#abc为需要建立会话的名称，后续detach之后需要通过这个名称来进行重连，自己记好
```
这时候我们已经进入tmux新建的会话中
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/202311052152555.png)  

到会话内部的使用，跟外部没什么区别，你可以把它看作就是一个普通的终端，可以通过`python test.py`来执行python文件等等。  
  
当我们需要执行一个比较久的文件时，我们可以现在tmux创建的会话中运行程序，然后输入
```bash
tmux detach
```
来detach当前会话，然后这时候你就会退出tmux会话，此时你可以关闭ssh连接，甚至是自己的电脑，然后去睡一觉，醒了之后，再次连接上ssh，然后通过指令:  
```bash
tmux attach -t abc
#abc是会话名称
```
即可重连回去，这时候就可以看运行结果咯。  
当然，如果你忘了你创建过多少会话，以及会话的名字，你可以通过指令:  
```bash
tmux ls
```
来查看已经创建的会话，以及对应的会话名称。  
其他更多指令，以及快捷键直接去参考阮一峰的教程。