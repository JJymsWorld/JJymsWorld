---
title: 在windows中给Spacevim设置右键启动
tags:
  - [neovim]
  - [spacevim]
  - [vim]
categories:
  - gallery
date: 2022-11-11 13:43:19
---

在windwos中设置直接从右键快速在spacevim中打开文件，并加载相应的配置。

具体步骤：
1. 按下`win+r`在运行弹窗中输入`regedit`进入注册表编辑器
2. 定位到`HKEY_CLASSES_ROOT > * > shell`
3. 在`shell`上点击右键， 新建项，名字输入`use spacevim open`或者随便你想输入的名字，这里主要是显示在右键菜单中的文字。然后再在这个项里面新建一个项，名字为`command`，并修改其默认值为：`"D:\Program Files\Neovim\bin\nvim-qt.exe" -qwindowgeometry 1310x650+20+20 "%1"`
4. 然后就可以在右键菜单中使用了，这里的启动命令指定了窗口大小，后面的参数可以自行调节。
