---
title: 关于neovim配置spacevim的lspconfig问题踩坑与解决方法记录
tags:
  - [neovim]
  - [spacevim]
  - [vim]
categories:
  - gallery
date: 2022-07-24 19:31:15 
---

在archlinux下做开发已经很长时间了,对于小型项目的开发一直使用的是vim和gvim. 偶然尝试了一下neovim, 发现其在UI性能方面相较于gvim更加强大, 界面滚动也更加丝滑, 随即产生了从Vim切换到neovim的想法. 由于我直接使用的Spacevim, 在切换到neovim时产生了一些问题, 主要是lspconfig的报错问题. 这里记录一下我搜索到的资料与解决方法, 以便后面再出现此类错误, 更容易修补.

首先贴上我在使用gvim时的Spacevim的配置文件代码:

```toml
#=============================================================================
# basic.toml --- basic configuration example for SpaceVim
# Copyright (c) 2016-2022 Wang Shidong & Contributors
# Author: Wang Shidong < wsdjeg at 163.com >
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
    vimcompatible = true
    disbaled_plugins = ["delimitMate"]
    expand_tab = true 
    relativenumber = false
    autocomplete_method = "coc"
    bootstrap_before = "myspacevim#before"
    default_indent = 4

[[custom_plugins]]
  repo = "jiangmiao/auto-pairs"
  merged = false

# [[custom_plugins]]
  # repo = "mhartington/oceanic-next"
  # merged = 0
[[custom_plugins]]
repo = "ryanoasis/vim-devicons"
merged = 0

# [[custom_plugins]]
# repo = "mattn/vim-treesitter"
# merged = 0

[[layers]]
name = "colorscheme"
# random_theme = true
[[layers]]
name = "sudo"

[[layers]]
name = "treesitter"
# Enable autocomplete layer
[[layers]]
name = 'autocomplete'
auto_completion_return_key_behavior = "complete"
auto_completion_tab_key_behavior = "cycle"

[[layers]]
name = 'shell'
default_position = 'top'
default_height = 30


[[layers]]
  name = "lang#javascript"

[[layers]]
  name = "lang#typescript"

[[layers]]
  name = "lang#python"

[[layers]]
  name = "lang#c"
  enable_clang_syntax_highlight = true
  [layer.clang_std]
    cpp = "c11"
[[layers]]
  name = "lang#go"

[[layers]]
  name = "lsp"
  enable_clients = [
    'clangd',
    'pyls',
    'gopls',
    'typescript-language-server'
  ]
  filetypes = [
    "c",
    "cpp",
    "python",
    "go",
    "javascript"
  ]
  [layers.override_cmd]
    c = ["clangd"]
    cpp = ["clangd"]
    python = ["pyls"]
    go = ["gopls"]
    javascript = ["typescript-language-server", "--stdio"]

[[layers]]
  name = "format"


```

下面是我修改完成使用neovim的配置文件代码:

```toml
#=============================================================================
# basic.toml --- basic configuration example for SpaceVim
# Copyright (c) 2016-2022 Wang Shidong & Contributors
# Author: Wang Shidong < wsdjeg at 163.com >
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
    vimcompatible = true
    disbaled_plugins = ["delimitMate"]
    expand_tab = true 
    relativenumber = false
    autocomplete_method = "nvim-cmp"
    bootstrap_before = "myspacevim#before"
    default_indent = 4
    snippet_engine = "ultisnips"

[[custom_plugins]]
  repo = 'https://github.com/hrsh7th/nvim-cmp.git'
  merged = 0

[[custom_plugins]]
  repo = "jiangmiao/auto-pairs"
  merged = false
[[custom_plugins]]
    repo = "chrisbra/csv.vim"
    merged = false

# [[custom_plugins]]
  # repo = "mhartington/oceanic-next"
  # merged = 0
[[custom_plugins]]
repo = "ryanoasis/vim-devicons"
merged = 0

# [[custom_plugins]]
# repo = "mattn/vim-treesitter"
# merged = 0

[[layers]]
name = "colorscheme"
# random_theme = true
[[layers]]
name = "sudo"

[[layers]]
name = "treesitter"
# Enable autocomplete layer
[[layers]]
name = 'autocomplete'
auto_completion_return_key_behavior = "complete"
auto_completion_tab_key_behavior = "cycle"

[[layers]]
name = 'shell'
default_position = 'top'
default_height = 30


[[layers]]
  name = "lang#javascript"

[[layers]]
  name = "lang#typescript"

[[layers]]
  name = "lang#python"

[[layers]]
  name = "lang#c"
  enable_clang_syntax_highlight = true
  [layer.clang_std]
    cpp = "c11"

[[layers]]
  name = "lang#go"

[[layers]]
  name = "lsp"
  enable_clients = [
    'clangd',
    # 'pyls',
    'pylsp'
    'gopls',
    # 'typescript-language-server'
    'tsserver'
  ]
  filetypes = [
    "c",
    "cpp",
    "python",
    "go",
    "javascript",
    "typescript"
  ]
  [layers.override_client_cmds]
    # c = ["clangd"]
    clangd = ["clangd"]
    pylsp = ["pylsp"]
    gopls = ["gopls", "serve"]
    tsserver = ["typescript-language-server", "--stdio"]

[[layers]]
  name = "format"


```

下面说明一下遇到的问题

首先是lspconfig问题

```shell
[dein] Error occurred while executing hook: nvim-lspconfig

[dein] Vim(lua):E5108: Error executing lua ...b/.SpaceVim/bundle/nvim-lspconfig/lua/lspconfig.lua:80: attempt to co

ncatenate local 'k' (a boolean value)

[dein] stack traceback:

[dein] ^I...b/.SpaceVim/bundle/nvim-lspconfig/lua/lspconfig.lua:80: in function '__index'

[dein] ^I[string ":lua"]:49: in main chunk
```

每次使用neovim都会报这个错误, 目前最新版本为0.7.2-3, 有的人切换到0.7.0-3即可正常运行, 然而在我这里却没有用, 无论我切换到什么版本都会报这个错误. 查找了很久的解决方案, 最终在spacevim 的google groups上面找到了一个有效的解决方案. 原链接见[此处](https://groups.google.com/g/spacevim/c/E5C9zXU_Xk8)

翻译过来的解决方法就是

在options里面设置自动不全的引擎为**nvim-cmp**, 由于Spacevim并没有内置nvim-cmp故需要我们手动添加配置让spacevim安装这个插件, 即下面的[[custom_plugins]]处. 这里修改完之后还需要修改lsp layers里面的`layers.override_cmd` 为`layers.override_client_cmds`并且其中的内容也需要修改,原本的是filetype对应client, 而修改后应为server_name对应client, 看不懂的可以仔细对比文章最上面的两个配置文件的区别.

```toml
[options] 
 #    disabled_plugins = ["nvim-lspconfig" ] # do not do that! it is not needed anymore.
    autocomplete_method = "nvim-cmp"
[[custom_plugins]]
    repo = "https://github.com/hrsh7th/nvim-cmp.git"
    merged = 0
[[layers]]
    name = "lsp"
    enabled_clients = [
#        'dartls',
        'gopls',
        'intelephense',
        'rust_analyzer',
        'tsserver'
    ]
    filetypes = [
#        "dart",
        "php",
        "rust",
        "go",
        "typescript",
        "javascript"
    ]
    [layers.override_client_cmds]
      intelephense = ["intelephense", "--stdio"]
#      dartls = [
#          "/opt/flutter/bin/cache/dart-sdk/bin/dart",
#          "/opt/flutter/bin/cache/dart-sdk/bin/snapshots/analysis_server.dart.snapshot",
#          "--lsp"
#      ]
      rust_analyzer = ["rust-analyzer"]
      gopls = ["gopls", "serve"]
      tsserver = ['typescript-language-server', '--stdio']

# and then per-language layers as usual, for example
[[layers]]
    name = "lang#php"
```

修改完成之后, 应该已经不会出现上面所显示的错误了. 但是还会继续遇到问题, 首先就是nvim-cmp的代码提示不完善, 它优先提示的是buffer, 而我们真正需要的是lsp的代码提示, 并且还存在的问题是代码提示中的选项, 我们无法通过方向键或者tab键来进行选择, 并且的并且我们还需要手动设置snippet engine, 否则我们在使用代码补全的时候,仍然会出现错误. 上述的这些问题需要修改nvim-cmp的配置文件, 这里直接放出我的配置文件, 参照着修改即可.

```lua
lua <<EOF
  -- Setup nvim-cmp.
  local cmp = require'cmp'

  cmp.setup({
    mapping = cmp.mapping.preset.insert({ --这里需要修改
      ['<C-d>'] = cmp.mapping(cmp.mapping.scroll_docs(-4), { 'i', 'c' }),
      ['<C-f>'] = cmp.mapping(cmp.mapping.scroll_docs(4), { 'i', 'c' }),
      ['<C-Space>'] = cmp.mapping(cmp.mapping.complete(), { 'i', 'c' }),
      ['<C-y>'] = cmp.config.disable, -- Specify `cmp.config.disable` if you want to remove the default `<C-y>` mapping.
      ['<C-e>'] = cmp.mapping({
        i = cmp.mapping.abort(),
        c = cmp.mapping.close(),
      }),
      ['<CR>'] = cmp.mapping.confirm({ select = true }), --这里添加tab选择功能
      ['<Tab>'] = function(fallback)
        if cmp.visible() then
          cmp.select_next_item()
        else
          fallback()
        end
      end,
      ['<S-Tab>'] = function(fallback)
        if cmp.visible() then
          cmp.select_prev_item()
        else
          fallback()
        end
      end,
    }), --到这里结束
    formatting = {
      format = require("lspkind").cmp_format({with_text = true, menu = ({
          buffer = "[Buffer]",
        })}),
    },
    sources = cmp.config.sources({ --这里修改使代码提示显示为lsp服务的提示,主要是顺序的修改
--      { name = 'path' },
      { name = 'nvim_lsp' }
    }, {
      { name = 'buffer' },
    }, {
      { name = 'path' },
    }, {
      { name = 'ultisnips' }
    }),
  snippet = { --这里是新增的 参照官方文档进行的修改 将UltiSnips设置为默认的snippet engine
    expand = function(args)
      vim.fn["UltiSnips#Anon"](args.body)
  end,
  },
})
-- The nvim-cmp almost supports LSP's capabilities so You should advertise it to LSP servers..
local capabilities = vim.lsp.protocol.make_client_capabilities()
capabilities = require('cmp_nvim_lsp').update_capabilities(capabilities)

-- The following example advertise capabilities to `clangd`.
require'lspconfig'.clangd.setup {
  capabilities = capabilities,
}
EOF
```

参考地址:

https://groups.google.com/g/spacevim?pli=1

https://groups.google.com/g/spacevim/c/Q8LBeNBBm60

https://groups.google.com/g/spacevim/c/E5C9zXU_Xk8

https://github.com/hrsh7th/nvim-cmp/issues/102

https://github.com/hrsh7th/nvim-cmp/wiki/List-of-sources

https://github.com/hrsh7th/nvim-cmp#basic-configuration
