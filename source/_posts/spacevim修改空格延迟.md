---
title: spacevim修改空格延迟
tags:
  - [spacevim]
  - [neovim]
  - [vim]
categories:
  - gallery
date: 2023-06-10 18:05:05
---
找到`.SpaceVim.d`文件夹，进入其中创建`autoload`文件夹，然后创建`myspacevim.vim`文件，填入以下内容。  
```vim
function! myspacevim#before() abort
    set timeoutlen=100
endfunction
```
然后在spacevim的配置文件中，在`option`下面加入一行代码.
```toml
bootstrap_before = "myspacevim#before"
```