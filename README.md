# 1 前言
本文所用配置以[LunarVim](https://github.com/LunarVim/LunarVim)的Nightly版本为基础。
Neovim版本: 0.9.0
插件管理器：lazy.nvim

你可以在LunarVim中，使用`<leader>Lc`打开`~/.config/lvim/config.lua`，将以下插件配置复制或替换到**合适**的位置`config.lua`中，然后重启lvim自动安装插件。

本文的配置将合理使用lazy.nvim的懒加载功能，只在需要的时候加载需要的插件，加快neovim的运行和加载速度，减少资源占用。

**适合阅读本文的人群：有一定的neovim配置经验**

## 1.1 完整配置文件
你可以在 https://github.com/ADkun/my-lvim-config/blob/main/config.lua 这个网址看到我最新的config.lua完整配置文件。

## 1.2 本文更好的阅读方式
**请访问 https://github.com/ADkun/lvim-config-suggest/blob/main/README.md 以更好的格式化方式查看本文**

# 2 插件
## 2.0 预配置
这些配置是为了在config.lua中提前声明一些变量，避免冗长的写法，你可以将这些配置提前写在config.lua的前部。

```lua
local keymap = lvim.builtin.which_key.mappings
local vkeymap = lvim.builtin.which_key.vmappings
```

## 2.1 filetype.nvim
推荐度：★★

主页：https://github.com/nathom/filetype.nvim

简介：
- filetype.nvim对neovim的filetype进行了优化，可以使打开文件时识别文件类型的速度更快。
-  在某些情况下，还可以修复由文件类型识别不正确导致的高亮不全问题。（如打开`.h`文件发现没有代码高亮）

使用方式：在config.lua中配置好，然后打开文件。

配置：
```lua
{
    "nathom/filetype.nvim",
    lazy = true,
    event = "User FileOpened",
    config = function()
        require("filetype").setup({
            overrides = {
                extensions = {
                    h = "cpp",
                },
            }
        })
    end
}
```
上面配置中的`extensions`表示将`.h`后缀的文件识别为`cpp`文件类型，使`.h`文件被neovim识别为cpp文件，使用cpp文件的高亮。
除了`extensions`以外，你还可以配置
- `literal`：匹配全文件名
- `complex`：使用正则表达式匹配路径和文件名
- `function_extensions`：对指定类型的文件设置回调函数
- `function_literal`：对指定全文件名的文件设置回调函数
- `function_complex`：对指定正则表达式匹配的文件设置回调函数
- `shebang`：对于包含了指定shebang的文件视为指定的文件类型，如`#! /usr/bin/dash`视为sh文件类型，以匹配不同类型的shell脚本文件。
具体配置方法见插件Github主页，里面有给出example。

## 2.2 trouble.nvim
推荐度：★★★

主页：https://github.com/folke/trouble.nvim
简介：
- trouble.nvim实现了trouble这种预览窗口类型（类似于quickfix, loclist），这种窗口的特点是切换项目时会将打开前的窗口实时预览到对应的位置上，退出trouble窗口时会回到原来的位置。
- trouble.nvim可以让你快速查看你的工作区、文件中的LSP警告列表。

使用方式：热键打开窗口

配置：
```lua
{
    "folke/trouble.nvim",
    lazy = true,
    cmd = { "TroubleToggle", "Trouble", "TroubleRefresh" },
    config = function()
        require("trouble").setup()
    end,
},
```
键位配置（which-key）：
```lua
keymap["t"] = { name = "Diagnostics" }
keymap["tt"] = { "<cmd>TroubleToggle<cr>", "trouble" }
keymap["tw"] = { "<cmd>TroubleToggle workspace_diagnostics<cr>", "workspace" }
keymap["td"] = { "<cmd>TroubleToggle document_diagnostics<cr>", "document" }
keymap["tq"] = { "<cmd>TroubleToggle quickfix<cr>", "quickfix" }
keymap["tl"] = { "<cmd>TroubleToggle loclist<cr>", "loclist" }
keymap["tr"] = { "<cmd>TroubleToggle lsp_references<cr>", "references" }
```
插件只会在你使用快捷键的时候才加载。
注：**which-key的配置方式需要你以`<leader>`键开头，比如上面配置的`keymap["tt"]`，你需要按`<leader>tt`。**
LunarVim的默认`<leader>`键是`空格`

## 2.3 leap.nvim
推荐度：★★★★★

主页：https://github.com/ggandor/leap.nvim
简介：
- leap.nvim可以让你的光标快速在可视范围内跳转。
- 你只需要按下引导键（自定义的第一个键），然后输入你想跳转到的地方的相邻2个字符，如有重复，输入最后一个提示字符即可。
- 可以配合其它neovim快捷键一起使用，如"c", "d", "y"

使用方式：见下面的快捷键介绍，也可以配合"c", "d", "y"等neovim原本的功能键一起使用。

配置：
```lua
{
    "ggandor/leap.nvim",
    lazy = true,
    keys = { "E", "R", "W", "dE", "dR", "yE", "yR", "cE", "cR" },
    config = function()
        require("leap").opts.highlight_unlabeled_phase_one_targets = true
        -- leap.add_default_mappings()
        vim.keymap.set({ "x", "o", "n" }, "E", "<Plug>(leap-forward-to)")
        vim.keymap.set({ "x", "o", "n" }, "R", "<Plug>(leap-backward-to)")
        vim.keymap.set({ "x", "o", "n" }, "W", "<Plug>(leap-from-window)")
    end,
},
```
快捷键介绍：
- `r`：在当前光标往右往下查找。
- `R`：在当前光标往左往上查找。
- `W`：多窗口情况下，可快速跳转到其它窗口的内容。

## 2.4 flit.nvim
推荐度：★★★★★

主页：https://github.com/ggandor/flit.nvim

简介：
- flit.nvim是leap.nvim同一个作者产出的，flit.nvim需要依赖于leap.nvim。
- flit.nvim可以增强neovim的"f", "F", "t", "T"这几个键的功能，同样是一个光标跳转插件。

使用方式：按"f", "F", "t", "T"，然后键入你想去的目标字符。如果本次没有到达你想去的目标，再次按下"f"或"t"，光标会跳到下一个目标。

配置：
```lua
{
    "ggandor/flit.nvim",
    lazy = true,
    keys = { "f", "F", "t", "T" },
    dependencies = { "ggandor/leap.nvim" },
    config = function()
        require("flit").setup({
            keys = { f = "f", F = "F", t = "t", T = "T", },
            labeled_modes = "v",
            multiline = true,
            opts = {},
        })
    end,
},
```

## 2.5 hop.nvim
推荐度：★★★★

主页：https://github.com/phaazon/hop.nvim

简介：
- hop.nvim与leap.nvim一样，同样是一款可视范围内光标跳转的插件。
- 与leap.nvim不同，这款插件有更多功能，并且支持单字符的跳转。

使用方式：以本配置为例，按"E"，然后键入可视范围内你想去的那个字符，最后输入1-2个提示字符就可以跳到目标位置了。
注：配置中以注释的方式将其它一些函数屏蔽掉了，仅保留单字符跳转功能，作为leap.nvim的补充。如有需要可去掉注释符自行配置。

配置：
```lua
{
    "phaazon/hop.nvim",
    lazy = true,
    keys = { "r" },
    config = function()
        require("hop").setup({})
        -- vim.api.nvim_set_keymap("n", "R", "<cmd>HopChar2<cr>", { silent = true })
        vim.api.nvim_set_keymap("n", "r", "<cmd>HopChar1<cr>", { silent = true })
        -- vim.api.nvim_set_keymap("n", "U", "<cmd>HopWord<cr>", { silent = true })
        -- vim.api.nvim_set_keymap("n", "C", "<cmd>HopLine<cr>", { silent = true })
        -- vim.api.nvim_set_keymap("n", "P", "<cmd>HopPattern<cr>", { silent = true })
    end,
},
```

## 2.6 nvim-ts-rainbow2
推荐度：★★★★

主页：https://github.com/HiPhish/nvim-ts-rainbow2

简介：
- nvim-ts-rainbow2基于nvim-treesitter，将配对的括号进行彩色的标注，方便辨认。

使用方式：配置即用

配置：
```lua
{
    "HiPhish/nvim-ts-rainbow2",
    -- Bracket pair rainbow colorize
    lazy = true,
    event = { "User FileOpened" },
},
```
然后在config.lua另外一个地方加上这一行（非插件的config函数里）
```lua
lvim.builtin.treesitter.rainbow.enable = true
```

## 2.7 nvim-treesitter-context
推荐度：★★★

主页：https://github.com/nvim-treesitter/nvim-treesitter-context

简介：
- nvim-treesitter-context是一款基于nvim-treesitter的上文文固定插件。
- 它可以将当前函数的函数头固定在neovim界面的前几行，让你知道当前在编辑的是什么类、函数或方法。

使用方式：配置即用

配置：
```lua
{
    "romgrk/nvim-treesitter-context",
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        require("treesitter-context").setup({
            enable = true,
            throttle = true,
            max_lines = 0, 
            patterns = {
                default = {
                    "class",
                    "function",
                    "method",
                },
            },
        })
    end,
},
```

## 2.8 harpoon
推荐度：★★★

主页：https://github.com/ThePrimeagen/harpoon

简介：当前项目的文件收藏夹

使用方式：快捷键使用
- `<leader>ohh`：在Telescope中打开Harpoon收藏的文件
- `<leader>oht`：打开Harpoon本身自带的收藏夹
- `<leader>ohf`：将当前文件加入到收藏夹
- `<leader>ohd`：将当前文件从收藏夹中删除
- `<leader>ohn`：切换到下一个收藏夹文件
- `<leader>ohp`：切换到上一个收藏夹文件

配置：
```lua
{
    "ThePrimeagen/harpoon",
    lazy = true,
    cmd = "Telescope harpoon marks",
    dependencies = {
        "nvim-lua/plenary.nvim",
    },
    config = function()
        require("harpoon").setup({})
        require("telescope").load_extension("harpoon")
    end,
}
```
快捷键配置：
```lua
keymap["oh"] = { name = "+Harpoon" }
keymap["ohf"] = { "<cmd>lua require('harpoon.mark').add_file()<cr>", "Add File" }
keymap["oht"] = { "<cmd>lua require('harpoon.ui').toggle_quick_menu()<cr>", "Toggle Menu" }
keymap["ohn"] = { "<cmd>lua require('harpoon.ui').nav_next()<cr>", "Next" }
keymap["ohp"] = { "<cmd>lua require('harpoon.ui').nav_prev()<cr>", "Prev" }
keymap["ohh"] = { "<cmd>Telescope harpoon marks<cr>", "Telescope Harpoon" }
keymap["ohd"] = { "<cmd>lua require('harpoon.mark').rm_file()<cr>", "Remove File" }
```


## 2.9 nvim-ts-context-commentstring
推荐度：★★★★

主页：https://github.com/JoosepAlviste/nvim-ts-context-commentstring

简介：
- 本插件基于nvim-treesitter，根据当前光标在文中的位置，配合Comment.nvim，自动选择合适的注释格式。

使用方式：配置即用。

配置：
```lua
{
    "JoosepAlviste/nvim-ts-context-commentstring",
    lazy = true,
    event = { "User FileOpened" },
},
```

## 2.10 goto-preview
推荐度：★★★★

主页：https://github.com/rmagatti/goto-preview

简介：本插件可以以浮动窗口的形式预览符号的定义、实现等信息。

使用方式：光标定位到目标符号，快捷键使用
- `gpd`：预览符号定义
- `gpi`：预览符号实现
- `gpr`：预览符号引用
- `gpt`：预览类型定义

```lua
{
    "rmagatti/goto-preview",
    lazy = true,
    keys = { "gp" },
    config = function()
        require("goto-preview").setup({
            width = 120,
            height = 25,
            default_mappings = true,
            debug = false,
            opacity = nil,
            post_open_hook = nil, 
            -- You can use "default_mappings = true" setup option
            -- Or explicitly set keybindings
            -- vim.cmd("nnoremap gpd <cmd>lua require('goto-preview').goto_preview_definition()<CR>")
            -- vim.cmd("nnoremap gpi <cmd>lua require('goto-preview').goto_preview_implementation()<CR>")
            -- vim.cmd("nnoremap gP <cmd>lua require('goto-preview').close_all_win()<CR>")
        })
    end,
},
```

## 2.11 nvim-lastplace
推荐度：★★★★★

主页：https://github.com/ethanholz/nvim-lastplace

简介：自动记忆当前文件位置，在下次打开时定位到上次位置。

使用方式：配置即用

配置：
```lua
{
    "ethanholz/nvim-lastplace",
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        require("nvim-lastplace").setup({
            lastplace_ignore_buftype = { "quickfix", "nofile", "help" },
            lastplace_ignore_filetype = {
                "gitcommit",
                "gitrebase",
                "svn",
                "hgcommit",
            },
            lastplace_open_folds = true,
        })
    end,
}
```

## 2.12 todo-comments.nvim
推荐度：★★★

主页：https://github.com/folke/todo-comments.nvim

简介：高亮注释中的关键词，按需安装。

使用方式：在注释中以以下关键词，并加上英文冒号，然后输入注释内容。
- `HACK`
- `NOTE`
- `TODO`
- `WARN`
- `WARNING`
- `BUG`
- `FIX`
- `PREF`

配置：
```lua
{
    "folke/todo-comments.nvim",
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        require("todo-comments").setup()
    end,
}
```

## 2.13 nvim-surround
推荐度：★★★★★

主页：https://github.com/kylechui/nvim-surround

简介：使用快捷键配合textobjects快速地添加/修改/删除各种包括符，如()、[]、{}、<>等。

使用方式：快捷键使用，下面举几个例子：
- `ysiw"`：将当前光标选中的单词用双引号包围，其中，`ys`是本插件的触发快捷键，`iw`是inner word的意思，这是neovim自带的一种textobject选中方式。
- `ds)`：将当前光标位置相邻的最内层的小括号删除。
- `cs)}`：将当前光标位置相邻的最内层的小括号替换为大括号。

配置：
```lua
{
    "kylechui/nvim-surround",
    lazy = true,
    keys = { "cs", "ds", "ys" },
    config = function()
        require("nvim-surround").setup({})
    end,
},
```

## 2.14 nvim-spectre
推荐度：★★★★

主页：https://github.com/nvim-pack/nvim-spectre

简介：搜索并替换项目中所有文件/当前文件/当前选中区域的内容

使用方式：快捷键使用
1. 在文件窗口中
- `<leader>osf`：在当前文件搜索
- `<leader>oss`：在当前目录（项目）中搜索，包括子文件夹
- `<leader>osv`：在当前选中区域搜索
- `<leader>osw`：在当前选中区域搜索单词

2. 在Spectre窗口中
- `<leader>R`：替换所有
- `<leader>rc`：替换当前光标所在行的匹配项
- `<cr>`：跳到该匹配项的原文件位置
- `<leader>q`：将所有匹配项发送到quickfix中
- `?`：查看所有按键

注：在Spectre窗口中是无法使用`<leader>q`来退出窗口的，因为有快捷键冲突。需要用`<C-w> c`来退出。

配置：
```lua
{
    "windwp/nvim-spectre",
    lazy = true,
    cmd = { "Spectre" },
    config = function()
        require("spectre").setup()
    end,
},
```
快捷键配置：
```lua
keymap["os"] = { name = "+Spectre" }
keymap["oss"] = { "<cmd>lua require('spectre').open()<cr>", "Spectre Open" }
vkeymap["osw"] = { "<cmd>lua require('spectre').open_visual({select_word=true})<cr>", "Spectre in Visual Word" }
vkeymap["osv"] = { "<esc><cmd>lua require('spectre').open_visual()<CR>", "Spectre in Visual" }
keymap["osf"] = { "viw<cmd>lua require('spectre').open_file_search()<CR>", "Spectre in File" }
```

## 2.15 nvim-bqf
推荐度：★★★★

主页：https://github.com/kevinhwang91/nvim-bqf

简介：增强你的quickfix窗口

使用方式：配置即可显示增强的quickfix窗口，然后在quickfix窗口内使用对应快捷键操作quickfix
- `z,`：将预览窗口的切换为全屏/半屏
- `o`：将当前quickfix条目以drop的方式（如果缓冲区没有就打开新缓冲区，如果有就切换到那个缓冲）
- 更多快捷键请查看配置以及参考Github主页

配置（并不完美，有兴趣的可以自行修改）：
```lua
{
    "kevinhwang91/nvim-bqf",
    -- quickfix preview and other functions
    lazy = true,
    event = { "WinNew" },
    config = function()
        require("bqf").setup({
            auto_enable = true,
            auto_resize_height = true,
            preview = {
                win_height = 12,
                win_vheight = 12,
                delay_syntax = 80,
                border_chars = { "┃", "┃", "━", "━", "┏", "┓", "┗", "┛", "█" },
                should_preview_cb = function(bufnr, qwinid)
                    local ret = true
                    local bufname = vim.api.nvim_buf_get_name(bufnr)
                    local fsize = vim.fn.getfsize(bufname)
                    if fsize > 100 * 1024 then
                        -- skip file size greater than 100k
                        ret = false
                    elseif bufname:match("^fugitive://") then
                        -- skip fugitive buffer
                        ret = false
                    end
                    return ret
                end,
            },
            func_map = {
                drop = "o",
                openc = "O",
                split = "<C-s>",
                tabdrop = "<C-t>",
                tabc = "",
                vsplit = "<C-v>",
                ptogglemode = "z,",
                stoggleup = "",
            },
            filter = {
                fzf = {
                    action_for = { ["ctrl-s"] = "split", ["ctrl-t"] = "tab drop" },
                    extra_opts = { "--bind", "ctrl-o:toggle-all", "--prompt", "> " },
                },
            },
        })
    end,
}
```

## 2.16 vim-matchup
推荐度：★★★

主页：https://github.com/andymass/vim-matchup

简介：在你光标位置将if..else if..else等语言内的对应标签高亮。扩展你的`%`键能力。

使用方式：配置即用，在if等可以配对的标签处点击`%`跳到下一个匹配处

配置：
```lua
{
    "andymass/vim-matchup",
    -- Highlight, jump between pairs like if..else
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        vim.g.matchup_matchparen_offscreen = { method = "popup" }
        lvim.builtin.treesitter.matchup.enable = true
    end,
},
```

## 2.17 persistence.nvim
推荐度：★★★★★

主页：https://github.com/folke/persistence.nvim

简介：自动保存你的session到文件中，在下次打开相同目录/项目时，你可以手动加载session恢复之前的工作状态。

使用方式：退出即自动保存，打开后使用`<leader>a`来加载session

配置：
```lua
{
    "folke/persistence.nvim",
    -- Restore last session of current dir
    lazy = true,
    event = "BufReadPre", -- this will only start session saving when an actual file was opened
    config = function()
        require("persistence").setup({
            dir = vim.fn.expand(vim.fn.stdpath("config") .. "/session/"),
            options = { "buffers", "curdir", "tabpages", "winsize" },
            pre_save = nil,
        })
    end,
}
```
快捷键配置：
```lua
keymap["a"] = { "<cmd>lua require('persistence').load()<cr>", "Restore last session for current dir" }

keymap["S"] = { name = "+Session" }
keymap["Sc"] = { "<cmd>lua require('persistence').load()<cr>", "Restore last session for current dir" }
keymap["Sl"] = { "<cmd>lua require('persistence').load({ last = true })<cr>", "Restore last session" }
keymap["SQ"] = { "<cmd>lua require('persistence').stop()<cr>", "Quit without saving session" }
```

## 2.18 better-escape.nvim
推荐度：★★★★★

主页：https://github.com/max397574/better-escape.nvim

简介：可以自定义任意2个按键来退出Insert模式。

使用方式：在Insert模式下，在200ms（可修改）内连续按下"jk"或"jl"（可修改）退出Insert模式。

注：如果电脑比较卡，200ms可能不够，可以修改为更长一些的时间。

配置：
```lua
{
    "max397574/better-escape.nvim",
    lazy = true,
    event = { "InsertEnter" },
    config = function()
        require("better_escape").setup({
            mapping = { "jk", "jl" },
            timeout = 200,
            clear_empty_lines = false,
            keys = "<Esc>",
        })
    end,
}
```

## 2.19 tabout.nvim
推荐度：★★★

主页：https://github.com/abecodes/tabout.nvim

简介：可以在Insert模式下，按`<Tab>`跳出括号。

使用方式：在Insert模式下，如果你的光标在括号或引号里面（可配置），按`<Tab>`键跳出括号
注：实际使用时，因为优先级比nvim-cmp低，所以在弹出补全窗口时，需要按`<C-e>`关闭补全窗口才能使用，所以体验并不是特别好。

配置：
```lua
{
    "abecodes/tabout.nvim",
    lazy = true,
    event = { "InsertEnter" },
    config = function()
        require("tabout").setup({
            tabkey = "<Tab>",
            backwards_tabkey = "<S-Tab>",
            act_as_tab = true,
            act_as_shift_tab = false,
            default_tab = "<C-t>",
            default_shift_tab = "<C-d>",
            enable_backwards = true,
            completion = true,
            tabouts = {
                { open = "'", close = "'" },
                { open = '"', close = '"' },
                { open = "`", close = "`" },
                { open = "(", close = ")" },
                { open = "[", close = "]" },
                { open = "{", close = "}" },
            },
            ignore_beginning = true,
            exclude = {
                "qf",
                "NvimTree",
                "toggleterm",
                "TelescopePrompt",
                "alpha",
                "netrw",
            },
        })
    end,
    after = { "nvim-cmp" },
},
```

## 2.20 smartyank.nvim
推荐度：★★

主页：https://github.com/ibhagwan/smartyank.nvim

简介：在"dd"等不希望将内容复制到系统剪贴板的时候不复制到系统剪贴板。支持在SSH等情况复制到系统剪贴板。

使用方式：配置即用。
注：可能是因为个人环境问题，没有实际体验到它的功能，但对性能没啥影响，还是装上吧。

配置：
```lua
{
    "ibhagwan/smartyank.nvim",
    lazy = true,
    event = "User FileOpened",
    config = function()
        require("smartyank").setup()
    end,
},
```

## 2.21 marks.nvim
推荐度：★★★★★

主页：https://github.com/chentoast/marks.nvim

简介：在左侧栏中显示这一行标记的marks，可以显示当前文件/所有缓冲区的所有marks/所有缓冲区的大写marks

使用方式：
- `mx`：neovim原本的mark方式进行标记，x为标记名。
- `m;`：自动对当前行按字母顺序进行小写标记
- `dmx`：x为标记名，删除某个标记名
- `mX`：X为大写标记名，大写的标记可以跨文件跳转。
- `<leader>oma`：显示所有标记（包括小写大写，在所有缓冲区中）
- `<leader>omb`：显示当前buffer中的所有标记（包括小写大写）
- `<leader>omg`：显示所有大写标记

配置：
```lua
{
    "chentoast/marks.nvim",
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        require("marks").setup({
            default_mappings = true,
            -- builtin_marks = { ".", "<", ">", "^" },
            cyclic = true,
            force_write_shada = false,
            refresh_interval = 250,
            sign_priority = { lower = 10, upper = 15, builtin = 8, bookmark = 20 },
            excluded_filetypes = {
                "qf",
                "NvimTree",
                "toggleterm",
                "TelescopePrompt",
                "alpha",
                "netrw",
            },
            bookmark_0 = {
                sign = "",
                virt_text = "hello world",
                annotate = false,
            },
            mappings = {},
        })
    end,
}
```
快捷键配置：
```lua
keymap["om"] = { name = "+Marks" }
keymap["oma"] = { "<cmd>MarksListAll<CR>", "Show All Marks" }
keymap["omb"] = { "<cmd>MarksListBuf<CR>", "Show Marks in Buffer" }
keymap["omg"] = { "<cmd>MarksListGlobal<CR>", "Show Marks Global" }
```

## 2.22 neodim
推荐度：★★★★★

主页：https://github.com/zbirenbaum/neodim

简介：将没有使用到的变量进行暗淡处理。

使用方式：配置即用

配置：
```lua
{
    "zbirenbaum/neodim",
    lazy = true,
    event = "LspAttach",
    config = function()
        require("neodim").setup({
            alpha = 0.75,
            blend_color = "#000000",
            update_in_insert = {
                enable = true,
                delay = 100,
            },
            hide = {
                virtual_text = true,
                signs = false,
                underline = false,
            },
        })
    end,
}
```

## 2.23 windows.nvim
推荐度：★★★★★

主页：https://github.com/anuvyklack/windows.nvim

简介：通过几个函数可以将当前neovim窗口进行全屏/垂直全屏/水平全屏/等分

使用方式：快捷键使用

配置：
```lua
{
    "anuvyklack/windows.nvim",
    lazy = true,
    cmd = { "WindowsMaximize", "WindowsMaximizeVertically", "WindowsMaximizeHorizontally", "WindowsEqualize" },
    dependencies = {
        "anuvyklack/middleclass",
    },
    config = function()
        require("windows").setup({
            autowidth = {
                enable = false,
            },
            ignore = {
                buftype = { "quickfix" },
                filetype = {
                    "NvimTree",
                    "neo-tree",
                    "undotree",
                    "gundo",
                    "qf",
                    "toggleterm",
                    "TelescopePrompt",
                    "alpha",
                    "netrw",
                },
            },
        })
    end,
}
```
快捷键配置：
```lua
keymap["m"] = { "<cmd>WindowsMaximize<cr>", "Window Maximize" }

keymap["z"] = { name = "+Windows" }
keymap["zm"] = { "<cmd>WindowsMaximize<cr>", "Window Maximize" }
keymap["zv"] = { "<cmd>WindowsMaximizeVertically<cr>", "Window Vertically Maximize" }
keymap["zh"] = { "<cmd>WindowsMaximizeHorizontally<cr>", "Window Horizontally Maximize" }
keymap["ze"] = { "<cmd>WindowsEqualize<cr>", "Window Equalize" }
```

## 2.24 nvim-notify
推荐度：★★★★★

主页：https://github.com/rcarriga/nvim-notify

简介：将各种提示显示为弹窗

使用方式：配置即用
- `<leader>onm`：显示messages（非noice功能）

注：只使用nvim-notify的话，只能将vim.notify重定向输出到notify窗口中，需要将所有消息重定向到notify的话，需要装noice.nvim
注2：为了性能，配置中将动画效果取消了，需要的可以自行开启（修改`stages`项）

配置：
```lua
{
    "rcarriga/nvim-notify",
    lazy = true,
    event = "VeryLazy",
    config = function()
        local notify = require("notify")
        notify.setup({
            -- "fade", "slide", "fade_in_slide_out", "static"
            stages = "static",
            on_open = nil,
            on_close = nil,
            timeout = 3000,
            fps = 1,
            render = "default",
            background_colour = "Normal",
            max_width = math.floor(vim.api.nvim_win_get_width(0) / 2),
            max_height = math.floor(vim.api.nvim_win_get_height(0) / 4),
            -- minimum_width = 50,
            -- ERROR > WARN > INFO > DEBUG > TRACE
            level = "TRACE",
        })

        vim.notify = notify
    end,
}
```

## 2.25 noice.nvim
推荐度：★★★★★

主页：https://github.com/folke/noice.nvim

简介：一个覆写了很多neovim原本UI的插件，很大幅度地提升了美观性。
- 将messages重定向到notify
- 将搜索、命令显示为独立的命令框，并去除最底部的显示栏，节省空间。
- 显示过往Notifications
- Telescope整合

使用方式：配置即用，快捷键呼出历史窗口等功能。
- `<leader>onn`：显示Notifications历史
- `<leader>ont`：在Telescope中显示Notifications历史
- `<leader>ond`：临时禁用Noice
- `<leader>one`：启用Noice

配置：
```lua
{
    "folke/noice.nvim",
    enabled = ENABLE_NOICE,
    lazy = true,
    event = "User FileOpened",
    dependencies = { "rcarriga/nvim-notify", "MunifTanjim/nui.nvim" },
    config = function()
        require("noice").setup({
            lsp = {
                progress = {
                    enabled = false,
                },
            },
            presets = {
                bottom_search = false,
                command_palette = true,
                long_message_to_split = true,
                inc_rename = false,
                lsp_doc_border = true,
            },
            messages = {
                enabled = true,
                view = "notify",
                view_error = "notify",
                view_warn = "notify",
                view_history = "messages",
                view_search = "virtualtext",
            },
            health = {
                checker = false,
            },
        })
    end,
}
```
快捷键配置：
```lua
keymap["on"] = { name = "+Notify" }
keymap["onn"] = { "<cmd>Notifications<cr>", "Show Notifications" }
keymap["ont"] = { "<cmd>Noice telescope<cr>", "Show Notifications in Telescope" }
keymap["onm"] = { "<cmd>messages<cr>", "Show Messages" }
keymap["ond"] = { "<cmd>NoiceDisable<cr>", "Noice Disable" }
keymap["one"] = { "<cmd>NoiceEnable<cr>", "Noice Enable" }
```

## 2.26 nvim-ufo
推荐度：★★★

主页：https://github.com/kevinhwang91/nvim-ufo

简介：启用neovim的折叠功能，在左侧栏显示可折叠的项目，并能够自定义折叠显示的内容

使用方式：快捷键使用
- `zm`：折叠整个文件的所有内容
- `zM`：同上
- `zr`：展开整个文件的所有内容
- `zR`：同上
- `zo`或`l`：展开当前光标所在行的1层折叠
- `zO`：展开当前光标所在行的所有折叠
- `zc`：当前光标所在行进行折叠
- `<leader>oud`：禁用Ufo
- `<leader>oue`：启用Ufo

注：配置中使用了`lazy = true`，意味着并不会在文件加载时加载本插件，你需要手动输入一次`zm`或`zr`以加载本插件。
注2：在多窗口的状态下，可能会有一些BUG，如保存时自动把另外窗口全部折叠了，这时候可以临时关闭UFO（`<leader>oud`）。

```lua
{
    "kevinhwang91/nvim-ufo",
    lazy = true,
    cmd = { "UfoDisable", "UfoEnable" },
    dependencies = {
        "kevinhwang91/promise-async",
    },
    config = function()
        vim.o.foldcolumn = "1" -- '0' is not bad
        vim.o.foldlevel = 99 -- Using ufo provider need a large value, feel free to decrease the value
        vim.o.foldlevelstart = 99
        vim.o.foldenable = true


        vim.cmd([[highlight AdCustomFold guifg=#bf8040]])
        local handler = function(virtText, lnum, endLnum, width, truncate)
            local newVirtText = {}
            local suffix = ("  %d "):format(endLnum - lnum)
            local sufWidth = vim.fn.strdisplaywidth(suffix)
            local targetWidth = width - sufWidth
            local curWidth = 0

            for _, chunk in ipairs(virtText) do
                local chunkText = chunk[1]
                local chunkWidth = vim.fn.strdisplaywidth(chunkText)
                if targetWidth > curWidth + chunkWidth then
                    table.insert(newVirtText, chunk)
                else
                    chunkText = truncate(chunkText, targetWidth - curWidth)
                    local hlGroup = chunk[2]
                    table.insert(newVirtText, { chunkText, hlGroup })
                    chunkWidth = vim.fn.strdisplaywidth(chunkText)
                    -- str width returned from truncate() may less than 2nd argument, need padding
                    if curWidth + chunkWidth < targetWidth then
                        suffix = suffix .. (" "):rep(targetWidth - curWidth - chunkWidth)
                    end
                    break
                end
                curWidth = curWidth + chunkWidth
            end

            -- Second line
            local lines = vim.api.nvim_buf_get_lines(0, lnum, lnum + 1, false)
            local secondLine = nil
            if #lines == 1 then
                secondLine = lines[1]
            elseif #lines > 1 then
                secondLine = lines[2]
            end
            if secondLine ~= nil then
                table.insert(newVirtText, { secondLine, "AdCustomFold" })
            end

            table.insert(newVirtText, { suffix, "MoreMsg" })

            return newVirtText
        end

        require("ufo").setup({
            provider_selector = function(bufnr, filetype, buftype)
                return { "treesitter", "indent" }
            end,
            fold_virt_text_handler = handler,
        })
    end,
}
```

## 2.27 nvim-window-picker
推荐度：★★★★

主页：https://github.com/s1n7ax/nvim-window-picker

简介：在打开的多个窗口快速跳转、交换。

使用方式：
- 键入`,w`，然后键入1个小写字母键选择窗口，跳转到对应窗口。
- 键入`,W`，然后键入1个小写字母键选择窗口，将当前焦点窗口与对应窗口交换。

配置：
```lua
{
    "s1n7ax/nvim-window-picker",
    lazy = true,
    event = { "WinNew" },
    config = function()
        local picker = require("window-picker")
        picker.setup({
            autoselect_one = true,
            include_current = false,
            filter_rules = {
                bo = {
                    filetype = { "neo-tree", "neo-tree-popup", "notify", "quickfix" },
                    buftype = { "terminal" },
                },
            },
            other_win_hl_color = "#e35e4f",
        })

        vim.keymap.set("n", ",w", function()
            local picked_window_id = picker.pick_window({
                include_current_win = true,
            }) or vim.api.nvim_get_current_win()
            vim.api.nvim_set_current_win(picked_window_id)
        end, { desc = "Pick a window" })

        -- Swap two windows using the awesome window picker
        local function swap_windows()
            local window = picker.pick_window({
                include_current_win = false,
            })
            local target_buffer = vim.fn.winbufnr(window)
            -- Set the target window to contain current buffer
            vim.api.nvim_win_set_buf(window, 0)
            -- Set current window to contain target buffer
            vim.api.nvim_win_set_buf(0, target_buffer)
        end

        vim.keymap.set("n", ",W", swap_windows, { desc = "Swap windows" })
    end,
}
```

## 2.28 symbols-outline.nvim
推荐度：★★★★★

主页：https://github.com/simrat39/symbols-outline.nvim

简介：以右侧栏形式显示当前文件的大纲、标题、符号

使用方式：快捷键打开侧栏
- `<leader>oo`：打开symbols-outline侧边栏
在侧栏的快捷键：
- `<cr>`：回车定位到符号位置，并焦点到目标窗口。
- `o`：定位到符号位置，但焦点保留在symbols-outline窗口。
- `<C-space>`：显示对应符号的hover窗口
- `K`：打开当前符号的预览窗口。
- `r`：重命名当前符号
- `a`：代码action
- `h`：当前光标处折叠
- `l`：当前光标处展开
- `P`：折叠所有
- `U`：展开所有折叠
- `Q`：重置折叠

注：需要LSP支持

配置：
```lua
{
"simrat39/symbols-outline.nvim",
    lazy = true,
    cmd = { "SymbolsOutline", "SymbolsOutlineOpen", "SymbolsOutlineClose" },
    config = function()
        local opts = {
            highlight_hovered_item = true,
            show_guides = true,
            auto_preview = false,
            position = "right",
            relative_width = true,
            width = 25,
            auto_close = false,
            show_numbers = false,
            show_relative_numbers = false,
            show_symbol_details = true,
            preview_bg_highlight = "Pmenu",
            autofold_depth = nil,
            auto_unfold_hover = true,
            fold_markers = { "", "" },
            wrap = false,
            keymaps = { -- These keymaps can be a string or a table for multiple keys
                close = { "<Esc>", "q" },
                goto_location = "<Cr>",
                focus_location = "o",
                hover_symbol = "<C-space>",
                toggle_preview = "K",
                rename_symbol = "r",
                code_actions = "a",
                fold = "h",
                unfold = "l",
                fold_all = "P",
                unfold_all = "U",
                fold_reset = "Q",
            },
            lsp_blacklist = {},
            symbol_blacklist = {},
            symbols = {
                File = { icon = "", hl = "@text.uri" },
                Module = { icon = "", hl = "@namespace" },
                Namespace = { icon = "", hl = "@namespace" },
                Package = { icon = "", hl = "@namespace" },
                Class = { icon = "𝓒", hl = "@type" },
                Method = { icon = "ƒ", hl = "@method" },
                Property = { icon = "", hl = "@method" },
                Field = { icon = "", hl = "@field" },
                Constructor = { icon = "", hl = "@constructor" },
                Enum = { icon = "", hl = "@type" },
                Interface = { icon = "ﰮ", hl = "@type" },
                Function = { icon = "", hl = "@function" },
                Variable = { icon = "", hl = "@constant" },
                Constant = { icon = "", hl = "@constant" },
                String = { icon = "𝓐", hl = "@string" },
                Number = { icon = "#", hl = "@number" },
                Boolean = { icon = "", hl = "@boolean" },
                Array = { icon = "", hl = "@constant" },
                Object = { icon = "", hl = "@type" },
                Key = { icon = "🔐", hl = "@type" },
                Null = { icon = "NULL", hl = "@type" },
                EnumMember = { icon = "", hl = "@field" },
                Struct = { icon = "𝓢", hl = "@type" },
                Event = { icon = "🗲", hl = "@type" },
                Operator = { icon = "+", hl = "@operator" },
                TypeParameter = { icon = "𝙏", hl = "@parameter" },
                Component = { icon = "󰡀", hl = "@function" },
                Fragment = { icon = "", hl = "@constant" },
            },
        }
        require("symbols-outline").setup(opts)
    end,
},
```

## 2.29 colorful-winsep.nvim
推荐度：★★★★

主页：https://github.com/nvim-zh/colorful-winsep.nvim

简介：打开多窗口时，在当前焦点窗口周围显示紫色的边框

使用方式：配置即用

配置：
```lua
{
    "nvim-zh/colorful-winsep.nvim",
    lazy = true,
    event = "WinNew",
    config = function()
        require("colorful-winsep").setup()
    end,
}
```

## 2.30 nvim-gomove
推荐度：★★★★

主页：https://github.com/booperlv/nvim-gomove

简介：通过快捷键将当前选中区域/当前行进行移动，并遵循缩进

使用方式：
1. Normal模式：
- `<M-h/l>`：向左/右移动当前光标处的字符
- `<M-j/k`：将当前行向下/上移动，遵循缩进

2. Visual模式：
- `<M-j/k/h/l>`：将当前选中区域向下/上/左/右移动，遵循缩进

配置：
```lua
{
    "booperlv/nvim-gomove",
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        require("gomove").setup({
            map_defaults = false,
            reindent = true,
            undojoin = true,
            move_past_end_col = false,
        })

        local map = vim.api.nvim_set_keymap
        map("n", "<M-h>", "<Plug>GoNSMLeft", { noremap = true, silent = true })
        map("n", "<M-j>", "<Plug>GoNSMDown", { noremap = true, silent = true })
        map("n", "<M-k>", "<Plug>GoNSMUp", { noremap = true, silent = true })
        map("n", "<M-l>", "<Plug>GoNSMRight", { noremap = true, silent = true })

        map("x", "<M-h>", "<Plug>GoVSMLeft", { noremap = true, silent = true })
        map("x", "<M-j>", "<Plug>GoVSMDown", { noremap = true, silent = true })
        map("x", "<M-k>", "<Plug>GoVSMUp", { noremap = true, silent = true })
        map("x", "<M-l>", "<Plug>GoVSMRight", { noremap = true, silent = true })

        map("x", "<C-h>", "<Plug>GoVSDLeft", { noremap = true, silent = true })
        map("x", "<C-j>", "<Plug>GoVSDDown", { noremap = true, silent = true })
        map("x", "<C-k>", "<Plug>GoVSDUp", { noremap = true, silent = true })
        map("x", "<C-l>", "<Plug>GoVSDRight", { noremap = true, silent = true })
    end,
}
```

## 2.31 search-replace.nvim
推荐度：★★★★★

主页：https://github.com/roobert/search-replace.nvim

简介：受够了手动输入`:%s/xxx/ccc/gcI`吗？这款插件可以使用快捷键一键帮你输入这些内容。
    这款插件是spectre插件的补充，你可以用这款插件或者spectre插件来达到全局文件替换的效果。

使用方式：快捷键使用
1. 当前缓冲区：
- `<leader>rs`：打开选择列表（个人不会用）
- `<leader>ro`：打开Cmdline，自动帮你键入好了`:%s//gcI`
- `<leader>rw`：打开Cmdline，自动帮你键入好了当前光标所在的word
- `<leader>rW`：打开Cmdline，自动帮你键入好了当前光标所在WORD
- `<leader>re`：...
- `<leader>rf`：...
2. 所有打开的缓冲区：同上，但是前缀是`<leader>rb`，只要多加一个`b`键即可
具体快捷键见下面的“快捷键配置”

配置：
```lua
{
    "roobert/search-replace.nvim",
    lazy = true,
    cmd = {
        "SearchReplaceSingleBufferVisualSelection",
        "SearchReplaceWithinVisualSelection",
        "SearchReplaceWithinVisualSelectionCWord",
        "SearchReplaceSingleBufferSelections",
        "SearchReplaceSingleBufferCWord",
        "SearchReplaceSingleBufferCWORD",
        "SearchReplaceSingleBufferCExpr",
        "SearchReplaceSingleBufferCFile",
        "SearchReplaceMultiBufferSelections",
        "SearchReplaceMultiBufferOpen",
        "SearchReplaceMultiBufferCWord",
        "SearchReplaceMultiBufferCWORD",
        "SearchReplaceMultiBufferCExpr",
        "SearchReplaceMultiBufferCFile",
    },
    config = function()
        require("search-replace").setup({
            default_replace_single_buffer_options = "gcI",
            default_replace_multi_buffer_options = "egcI",
        })
    end,
}
```
快捷键配置：
```lua
-- search.replace.nvim config BEGIN
keymap["r"] = {
    name = "SearchReplaceSingleBuffer",
    s = { "<CMD>SearchReplaceSingleBufferSelections<CR>", "SearchReplaceSingleBuffer [s]elction list" },
    o = { "<CMD>SearchReplaceSingleBufferOpen<CR>", "[o]pen" },
    w = { "<CMD>SearchReplaceSingleBufferCWord<CR>", "[w]ord" },
    W = { "<CMD>SearchReplaceSingleBufferCWORD<CR>", "[W]ORD" },
    e = { "<CMD>SearchReplaceSingleBufferCExpr<CR>", "[e]xpr" },
    f = { "<CMD>SearchReplaceSingleBufferCFile<CR>", "[f]ile" },
    b = {
        name = "SearchReplaceMultiBuffer",
        s = { "<CMD>SearchReplaceMultiBufferSelections<CR>", "SearchReplaceMultiBuffer [s]elction list" },
        o = { "<CMD>SearchReplaceMultiBufferOpen<CR>", "[o]pen" },
        w = { "<CMD>SearchReplaceMultiBufferCWord<CR>", "[w]ord" },
        W = { "<CMD>SearchReplaceMultiBufferCWORD<CR>", "[W]ORD" },
        e = { "<CMD>SearchReplaceMultiBufferCExpr<CR>", "[e]xpr" },
        f = { "<CMD>SearchReplaceMultiBufferCFile<CR>", "[f]ile" },
    },
}
lvim.keys.visual_block_mode["<C-r>"] = [[<CMD>SearchReplaceSingleBufferVisualSelection<CR>]]
lvim.keys.visual_block_mode["<C-s>"] = [[<CMD>SearchReplaceWithinVisualSelection<CR>]]
lvim.keys.visual_block_mode["<C-b>"] = [[<CMD>SearchReplaceWithinVisualSelectionCWord<CR>]]
vim.o.inccommand = "split"
-- search.replace.nvim config END
```

## 2.32 trailblazer.nvim
推荐度：★★★★

主页：https://github.com/LeonHeidelbach/trailblazer.nvim

简介：一款很强大的临时轨迹标记插件，它可以使用快捷键保存你当前的位置，然后安心地把光标移到其它地方，之后再按快捷键按顺序跳回原来记录的位置。

使用方式：快捷键使用。
- `<A-s>`：在当前行保存轨迹坐标
- `<A-d>`：回到上一个保存的轨迹坐标，并删除该坐标
- `<A-o>`：用quickfix打开所有轨迹坐标的列表
- `<A-L>`：删除所有轨迹坐标
- `<A-p>`：在上一个保存的轨迹坐标处粘贴
- `<A-P>`：在所有保存的轨迹坐标处粘贴
更多快捷键见配置，以及Github文档。

配置：
```lua
{
    "LeonHeidelbach/trailblazer.nvim",
    lazy = true,
    keys = { "<A-s>", "<A-d>" },
    config = function()
        -- local HOME = os.getenv("HOME")
        require("trailblazer").setup({
            auto_save_trailblazer_state_on_exit = false,
            auto_load_trailblazer_state_on_enter = false,
            -- custom_session_storage_dir = HOME .. "/.local/share/trail_blazer_sessions/",
            trail_options = {
                mark_symbol = "•", --  will only be used if trail_mark_symbol_line_indicators_enabled = true
                newest_mark_symbol = "󰝥", -- disable this mark symbol by setting its value to ""
                cursor_mark_symbol = "󰺕", -- disable this mark symbol by setting its value to ""
                next_mark_symbol = "󰬦", -- disable this mark symbol by setting its value to ""
                previous_mark_symbol = "󰬬", -- disable this mark symbol by setting its value to ""
            },
            mappings = {
                nv = {
                    motions = {
                        new_trail_mark = "<A-s>",
                        track_back = "<A-d>",
                        peek_move_next_down = "<A-J>",
                        peek_move_previous_up = "<A-K>",
                        move_to_nearest = "<A-n>",
                        toggle_trail_mark_list = "<A-o>",
                    },
                    actions = {
                        delete_all_trail_marks = "<A-L>",
                        paste_at_last_trail_mark = "<A-p>",
                        paste_at_all_trail_marks = "<A-P>",
                        set_trail_mark_select_mode = "<A-t>",
                        switch_to_next_trail_mark_stack = "<A-.>",
                        switch_to_previous_trail_mark_stack = "<A-,>",
                        set_trail_mark_stack_sort_mode = "<A-S>",
                    },
                },
            },
            quickfix_mappings = { -- rename this to "force_quickfix_mappings" to completely override default mappings and not merge with them
                -- nv = {
                -- 	motions = {
                -- 		qf_motion_move_trail_mark_stack_cursor = "<CR>",
                -- 	},
                -- 	actions = {
                -- 		qf_action_delete_trail_mark_selection = "d",
                -- 		qf_action_save_visual_selection_start_line = "v",
                -- 	},
                -- 	alt_actions = {
                -- 		qf_action_save_visual_selection_start_line = "V",
                -- 	},
                -- },
                -- v = {
                -- 	actions = {
                -- 		qf_action_move_selected_trail_marks_down = "<C-j>",
                -- 		qf_action_move_selected_trail_marks_up = "<C-k>",
                -- 	},
                -- },
            },
        })
    end,
}
```

## 2.33 nvim-recorder
推荐度：★★★★★

主页：https://github.com/chrisgrieser/nvim-recorder

简介：大幅简化和增强neovim自身的宏功能。允许你编辑、复制宏的内容以及设置断点

使用方法：快捷键使用
- `q`：开始/结束录制宏
- `Q`：执行当前选择的宏
- `<A-q>`：切换宏槽位
- `cq`：编辑当前槽位的宏
- `yq`：复制当前槽位的宏

注：本配置仅使用了"u", "i", "o"三个槽位，如果需要更多槽位可以自行配置`slot`项。

配置：
```lua
{
    "chrisgrieser/nvim-recorder",
    lazy = true,
    keys = { "q", "Q", "<A-q>", "cq", "yq" },
    config = function()
        require("recorder").setup({
            slots = { "u", "i", "o" },
            mapping = {
                startStopRecording = "q",
                playMacro = "Q",
                switchSlot = "<A-q>",
                editMacro = "cq",
                yankMacro = "yq",
                -- addBreakPoint = "##",
            },
        })
    end,
}
```

## 2.34 nvim-various-textobjs
推荐度：★★★★

主页：https://github.com/chrisgrieser/nvim-various-textobjs

简介：为neovim新增很多textobjects，它们可以丰富你的快捷键选中、复制、修改等操作的体验。

使用方式：快捷键使用（以选中功能"v"来举例，可以替换为"c"（删除并修改）、"d"（删除）、"y"复制等）（`i`可以替换为`a`，`i`表示"inner"，`a`表示"outer"，如`va}`会选中包括`}`本身的内容，而`vi}`则不会）
- `viS`：选中当前光标下的子word（如`VimEnter`，我们使用`viw`会选中整个`VimEnter`，但`viS`只会选中`Enter`或`Vim`）
- `vii`：选中当前相同缩进的所有行
- `vR`：选中当前相同缩进往后剩余的行
- `v%`：选中当前光标下对应的括号结束位置
- `vr`：选中剩余的段落
- `vgG`：选中整个文件
- `v_`：选中整行有字符的部分（除去空白字符）
- `viv`：选中key-value的value部分
- `vik`：选中key-value的key部分
- `vL`：选中URL
- `vin`：选中数字部分
- `v!`：选中诊断部分（需要LSP）
- `vil`：选中markdown的链接
- `viC`：选中markdown的代码块部分
- `vic`：选中css选择器
- `vi/`：选中javascript的正则表达式pattern
- `viD`：选中双中括号内容`[[]]`

注：可以修改`lookForwardLines`的数量来改变我们使用textobjects时以当前光标为起点查找的行数。

配置：
```lua
{
    "chrisgrieser/nvim-various-textobjs",
    lazy = true,
    event = { "User FileOpened" },
    config = function()
        require("various-textobjs").setup({
            useDefaultKeymaps = true,
            lookForwardLines = 10,
        })
        -- example: `an` for outer subword, `in` for inner subword
        vim.keymap.set({ "o", "x" }, "aS", function()
            require("various-textobjs").subword(false)
        end)
        vim.keymap.set({ "o", "x" }, "iS", function()
            require("various-textobjs").subword(true)
        end)
    end,
}
```

## 2.35 nvim-treesitter-textobjects
推荐度：★★★★

主页：https://github.com/nvim-treesitter/nvim-treesitter-textobjects

简介：基于nvim-treesitter的textobjects，可以帮你选中class、function等语法内容

使用方式：快捷键使用，类似于nvim-various-textobjs（以选中模式`v`举例）
1. 选择
- `vaf`：选中当前函数
- `vac`：选中当前类内容
- `vas`：选中当前scope
- `vad`：选中当前条件（if..else）

2. 跳转
- `]m`：跳到下一个函数的开始处
- `]]`：跳到下一个类的开始处
- `]s`：跳到下一个scope的开始处
- `]z`：跳到下一个fold的开始处

- `]M`：跳到下一个函数的结尾处
- `][`：跳到下一个类的结尾处

- `[m`：跳到上一个函数的开始处
- `[[`：跳到上一个类的开始处

- `[M`：跳到上一个函数的结尾处
- `[]`：跳到上一个类的结尾处

- `]d`：跳到下一个条件
- `[d`：跳到上一个条件

注：更多配置和用法见Github主页

配置：
```lua
{
    "nvim-treesitter/nvim-treesitter-textobjects",
    lazy = true,
    event = { "User FileOpened" },
    after = "nvim-treesitter",
    dependencies = { "nvim-treesitter/nvim-treesitter" },
    config = function()
        require("nvim-treesitter.configs").setup({
            textobjects = {
                select = {
                    enable = true,
                    lookahead = true,
                    keymaps = {
                        ["af"] = "@function.outer",
                        ["if"] = "@function.inner",
                        ["ac"] = "@class.outer",
                        ["ic"] = { query = "@class.inner", desc = "Select inner part of a class region" },
                        ["as"] = { query = "@scope", query_group = "locals", desc = "Select language scope" },
                        ["id"] = "@conditional.inner",
                        ["ad"] = "@conditional.outer",
                    },
                    selection_modes = {
                        ["@parameter.outer"] = "v", -- charwise
                        ["@function.outer"] = "V", -- linewise
                        ["@class.outer"] = "<c-v>", -- blockwise
                    },
                    include_surrounding_whitespace = false,
                },
                move = {
                    enable = true,
                    set_jumps = true,
                    goto_next_start = {
                        ["]m"] = "@function.outer",
                        ["]]"] = { query = "@class.outer", desc = "Next class start" },
                        --
                        -- You can use regex matching and/or pass a list in a "query" key to group multiple queires.
                        ["]o"] = "@loop.*",
                        -- ["]o"] = { query = { "@loop.inner", "@loop.outer" } }
                        --
                        -- You can pass a query group to use query from `queries/<lang>/<query_group>.scm file in your runtime path.
                        -- Below example nvim-treesitter's `locals.scm` and `folds.scm`. They also provide highlights.scm and indent.scm.
                        ["]s"] = { query = "@scope", query_group = "locals", desc = "Next scope" },
                        ["]z"] = { query = "@fold", query_group = "folds", desc = "Next fold" },
                    },
                    goto_next_end = {
                        ["]M"] = "@function.outer",
                        ["]["] = "@class.outer",
                    },
                    goto_previous_start = {
                        ["[m"] = "@function.outer",
                        ["[["] = "@class.outer",
                    },
                    goto_previous_end = {
                        ["[M"] = "@function.outer",
                        ["[]"] = "@class.outer",
                    },
                    -- Below will go to either the start or the end, whichever is closer.
                    -- Use if you want more granular movements
                    -- Make it even more gradual by adding multiple queries and regex.
                    goto_next = {
                        ["]d"] = "@conditional.outer",
                    },
                    goto_previous = {
                        ["[d"] = "@conditional.outer",
                    },
                },
                swap = {
                    enable = false,
                    swap_next = {
                        ["<leader>a"] = "@parameter.inner",
                    },
                    swap_previous = {
                        ["<leader>A"] = "@parameter.inner",
                    },
                },
            },
        })
        local ts_repeat_move = require("nvim-treesitter.textobjects.repeatable_move")

        -- Repeat movement with ; and ,
        -- ensure ; goes forward and , goes backward regardless of the last direction
        vim.keymap.set({ "n", "x", "o" }, ";", ts_repeat_move.repeat_last_move_next)
        vim.keymap.set({ "n", "x", "o" }, ",", ts_repeat_move.repeat_last_move_previous)

        -- vim way: ; goes to the direction you were moving.
        -- vim.keymap.set({ "n", "x", "o" }, ";", ts_repeat_move.repeat_last_move)
        -- vim.keymap.set({ "n", "x", "o" }, ",", ts_repeat_move.repeat_last_move_opposite)

        -- Optionally, make builtin f, F, t, T also repeatable with ; and ,
        -- vim.keymap.set({ "n", "x", "o" }, "f", ts_repeat_move.builtin_f)
        -- vim.keymap.set({ "n", "x", "o" }, "F", ts_repeat_move.builtin_F)
        -- vim.keymap.set({ "n", "x", "o" }, "t", ts_repeat_move.builtin_t)
        -- vim.keymap.set({ "n", "x", "o" }, "T", ts_repeat_move.builtin_T)
    end,
}
```

## 2.36 nvim-treesitter-textsubjects
推荐度：★★★

主页：https://github.com/RRethy/nvim-treesitter-textsubjects

简介：根据光标位置自动决定要选中什么textobject

使用方式：快捷键使用（以v选中模式举例）
- `v.`：根据光标位置，智能选择
- `v,`：选中上一次选中的范围
- `v;`：选中容器外围
- `vi;`：选中容器内

注：在某些情况下貌似有点卡，可能是因为nvim-treesitter的树比较长的缘故，但是小文件还是很快的

配置：
```lua
{
    "RRethy/nvim-treesitter-textsubjects",
    lazy = true,
    event = { "User FileOpened" },
    after = "nvim-treesitter",
    dependencies = { "nvim-treesitter/nvim-treesitter" },
    config = function()
        require("nvim-treesitter.configs").setup({
            textsubjects = {
                enable = true,
                prev_selection = ",",
                keymaps = {
                    ["."] = "textsubjects-smart",
                    [";"] = "textsubjects-container-outer",
                    ["i;"] = "textsubjects-container-inner",
                },
            },
        })
    end,
},
```

# 3 其它配置
## 3.1 snip
在`~/.config/lvim`下创建一个`snippets`目录，然后在里面创建一个`package.json`，配置好指定格式的内容，之后编辑对应的json文件就可以为对应的文件类型添加snippets了

参照 https://github.com/ADkun/my-lvim-config 的`snippets`目录

## 3.2 formatter和linter
在Mason插件中下载的formatter和linter，需要我们手动将它们对应到文件类型上。
以下是我的配置项，可以参考配置。

```lua
-- -- linters and formatters <https://www.lunarvim.org/docs/languages#lintingformatting>
local formatters = require("lvim.lsp.null-ls.formatters")
formatters.setup({
	{ command = "stylua" },
	{
		command = "black",
		filetypes = { "python" },
	},
	{
		command = "prettier",
		extra_args = { "--print-width", "100" },
		filetypes = { "typescript", "typescriptreact" },
	},
})
local linters = require("lvim.lsp.null-ls.linters")
linters.setup({
	{ command = "flake8", filetypes = { "python" } },
	{
		command = "shellcheck",
		args = { "--severity", "warning" },
	},
	{
		command = "luacheck",
		filetypes = { "lua" },
	},
	-- {
	-- 	command = "cpplint",
	-- 	filetypes = { "cpp", "c" },
	-- },
})
```

## 3.3 代码补全
LunarVim自带的代码补全其实已经足够大部分场景下使用了，但是我们还可以安装一些补充插件。

```lua
{ "lukas-reineke/cmp-under-comparator", lazy = true },
{
    "f3fora/cmp-spell",
    lazy = true,
    config = function()
      vim.opt.spell = true
      vim.opt.spelllang:append "en_us"
    end,
},
{
    "ray-x/cmp-treesitter",
    lazy = true,
},
```

对于`cmp-under-comparator`，使用快捷键`<leader>Lf`打开cmp.lua，
然后在`lvim.builtin.cmp`中加入一项：
```lua
sorting = {
  comparators = {
    cmp.config.compare.offset,
    cmp.config.compare.exact,
    cmp.config.compare.score,
    require("cmp-under-comparator").under,
    cmp.config.compare.kind,
    cmp.config.compare.sort_text,
    cmp.config.compare.length,
    cmp.config.compare.order,
  },
},
```

## 3.4 主题推荐
推荐主题：OceanicNext

```lua
{
    "mhartington/oceanic-next",
    priority = 1000,
    lazy = lvim.colorscheme ~= "OceanicNext",
},
```
在config.lua中添加这一行作为lvim的主题：
```lua
lvim.colorscheme = "OceanicNext"
```
