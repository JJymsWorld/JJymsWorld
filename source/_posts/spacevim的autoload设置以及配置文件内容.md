---
title: spacevim的autoload设置以及配置文件内容
tags:
  - [spacevim]
  - [neovim]
  - [vim]
categories:
  - gallery
date: 2023-10-07 22:09:41
---
`~\Spacevim.d\autoload`下的预加载配置文件  
```vim
function! myspacevim#before() abort
        set timeoutlen=100
    let g:python3_host_prog = expand("C:\\Users\\17189\\miniconda3\\python.exe")
endfunction
```
上面的`g:python3_host_prog`在windows系统中使用如果不指定会出现找不到python环境，并且python环境目录下必须要有`python3.exe`，可以复制一个出来，在linux系统下使用时暂时未发现这个问题。  
下面是`spacevim`的配置文件
```vim
#=============================================================================
# basic.toml --- basic configuration example for SpaceVim
# Copyright (c) 2016-2023 Wang Shidong & Contributors
# Author: Wang Shidong < wsdjeg@outlook.com >
# URL: https://spacevim.org
# License: GPLv3
#=============================================================================

# All SpaceVim option below [option] section
[options]
    # set spacevim theme. by default colorscheme layer is not loaded,
    # if you want to use more colorscheme, please load the colorscheme
    # layer
    colorscheme = "onedark"
    colorscheme_bg = "dark"
    # Disable guicolors in basic mode, many terminal do not support 24bit
    # true colors
    enable_guicolors = true
    # Disable statusline separator, if you want to use other value, please
    # install nerd fonts
    statusline_separator = "nil"
    statusline_iseparator = "bar"
    buffer_index_type = 4
    windows_index_type = 3
    enable_tabline_filetype_icon = false
    enable_statusline_mode = false
    statusline_unicode = false
    # Enable vim compatible mode, avoid changing origin vim key bindings
    bootstrap_before = "myspacevim#before"
    vimcompatible = true
    relativenumber = false
    default_indent = 4
    expand_tab = true
    disabled_plugins = ["delimitMate"]
    # autocomplete_method = "coc"

[[custom_plugins]]
repo = "jiangmiao/auto-pairs"
merged = false

# Enable autocomplete layer
[[layers]]
name = 'autocomplete'
auto_completion_return_key_behavior = "complete"
auto_completion_tab_key_behavior = "cycle"

[[layers]]
name = 'shell'
default_position = 'top'
default_height = 30
default_shell = "terminal"

[[layers]]
name = "colorscheme"

[[layers]]
name = "sudo"

[[layers]]
name = "lang#c"
enable_clang_syntax_highlight = true

[[layers]]
name = "lang#python"



[[layers]]
name = "lsp"
enabled_clients = [
    'clangd',
    'pylsp',
]
    [layers.clang_std]
        cpp = "c11"
filetypes = [
    "c",
    "cpp",
    "python",
]
    [layers.override_cmd]
        c = ["clangd"]
        cpp = ["clangd"]
        python = ["pylsp"]

[[layers]]
name = "format"

# [[layers]]
# name = "lang#ipynb"
```
  
PS:`Spacevim`最新的版本在直接使用内部的代码执行会有问题，建议使用旧版本或者等待2.3.0之后的版本再继续使用。