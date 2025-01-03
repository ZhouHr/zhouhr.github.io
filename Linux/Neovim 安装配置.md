### Nvim 配置基础知识

#### Lua 语言

在配置 `Nvim` 的时候，**尽可能**用 Lua 语言写配置，因此你有必要了解一下 Lua 的基本语法和语义。可以快速浏览一下 [Learn Lua in Y minutes](https://learnxinyminutes.com/docs/lua/) 了解大概

#### 配置文件路径

`Nvim` 的配置目录在 `~/.config/nvim` 下。在 Linux/Mac 系统上，`Nvim` 会默认读取 `~/.config/nvim/init.lua` 文件，**理论上**来说可以将所有配置的东西都放在这个文件里面，但这样不是一个好的做法，因此我划分不同的文件和目录来分管不同的配置

首先看下按照本篇教程配置 `Nvim` 之后，目录结构看起来会是这样的

```bash
nvim
├── init.lua
└── lua
    ├── colorscheme.lua
    ├── config
    │   └── nvim-cmp.lua
    ├── keymaps.lua
    ├── lsp.lua
    ├── options.lua
    └── plugins.lua

```

**解释如下**

-   `init.lua` 为 `Nvim` 配置的 Entry point，我们主要**用来导入其他 `*.lua` 文件**
    -   `colorscheme.lua` 配置主题
    -   `keymaps.lua` 配置按键映射
    -   `lsp.lua` 配置 LSP
    -   `options.lua` 配置选项
    -   `plugins.lua` 配置插件
-   `config` 用于**存放各种插件自身的配置**，文件名为插件的名字，这样比较好找。_这里的 `nvim-cmp.lua` 就是 `nvim-cmp` 插件的配置文件_
-   `lua` 目录。当我们在 Lua 里面调用 `require` 加载模块（文件）的时候，它会自动在 `lua` 文件夹里面进行搜索
    -   **将路径分隔符从 `/` 替换为 `.`，然后去掉 `.lua` 后缀就得到了 `require` 的参数格式**
    -   _比如要导入上面的 `nvim-cmp.lua` 文件，可以用 `require('config.nvim-cmp')`_

#### 选项配置

主要用到的就是 `vim.g`、`vim.opt`、`vim.cmd` 等，我制造了一个快速参照对比的表格

| In `Vim` | In `Nvim` | Note |
| --- | --- | --- |
| `let g:foo = bar` | `vim.g.foo = bar` |  |
| `set foo = bar` | `vim.opt.foo = bar` | `set foo` = `vim.opt.foo = true` |
| `some_vimscript` | `vim.cmd(some_vimscript)` |  |

#### 按键配置

在 `Nvim` 里面进行按键绑定的语法如下，具体的解释可以看 `:h vim.keymap.set`

```Lua
vim.keymap.set(<mode>, <key>, <action>, <opts>)

```

### 从零开始配置 Nvim

在阅读了前面一些配置基础之后，现在我们可以从头开始，由简到易一步步配置 `Nvim` 了

#### 安装 Nvim

参考[官方文档](https://github.com/neovim/neovim/wiki/)


在安装完成之后，如果 `~/.config/nvim` 目录不存在，创建目录并新建 `init.lua` 文件

```bash
$ mkdir ~/.config/nvim
$ mkdir ~/.config/nvim/lua
$ touch ~/.config/nvim/init.lua

```

> 💡 **配置文件编辑保存之后，重启 `Nvim` 就能看到效果，后面默认每次小章节配置完成后就重启**

#### 选项配置

选项配置功能一览：

-   默认采用系统剪贴板，同时支持鼠标操控 `Nvim`
-   Tab 和空格的换算
-   UI 界面
-   “智能”搜索

新建 `~/.config/nvim/lua/options.lua` 文件并加入如下内容

```Lua
-- Hint: use `:h <option>` to figure out the meaning if needed
vim.opt.clipboard = 'unnamedplus' -- use system clipboard
vim.opt.completeopt = { 'menu', 'menuone', 'noselect' }
vim.opt.mouse = 'a' -- allow the mouse to be used in Nvim

-- Tab
vim.opt.tabstop = 4 -- number of visual spaces per TAB
vim.opt.softtabstop = 4 -- number of spacesin tab when editing
vim.opt.shiftwidth = 4 -- insert 4 spaces on a tab
vim.opt.expandtab = true -- tabs are spaces, mainly because of python

-- UI config
vim.opt.number = true -- show absolute number
vim.opt.relativenumber = true -- add numbers to each line on the left side
vim.opt.cursorline = true -- highlight cursor line underneath the cursor horizontally
vim.opt.splitbelow = true -- open new vertical split bottom
vim.opt.splitright = true -- open new horizontal splits right
-- vim.opt.termguicolors = true        -- enabl 24-bit RGB color in the TUI
vim.opt.showmode = false -- we are experienced, wo don't need the "-- INSERT --" mode hint

-- Searching
vim.opt.incsearch = true -- search as characters are entered
vim.opt.hlsearch = false -- do not highlight matches
vim.opt.ignorecase = true -- ignore case in searches by default
vim.opt.smartcase = true -- but make it case sensitive if an uppercase is entered

```

然后打开 `init.lua`，用 `require` 导入刚才写的 `options.lua` 文件

```Lua
require('options')        

```

#### 按键配置

按键功能一览：

-   用 `<C-h/j/k/l>` 快速在多窗口之间移动光标
-   用 `Ctrl` + 方向键进行窗口大小的调整
-   选择模式下可以一直用 `Tab` 或者 `Shift-Tab` 改变缩进

新建 `~/.config/nvim/lua/keymaps.lua` 文件并放入如下内容

```Lua
-- define common options
local opts = {
    noremap = true,      -- non-recursive
    silent = true,       -- do not show message
}

-----------------
-- Normal mode --
-----------------

-- Hint: see `:h vim.map.set()`
-- Better window navigation
vim.keymap.set('n', '<C-h>', '<C-w>h', opts)
vim.keymap.set('n', '<C-j>', '<C-w>j', opts)
vim.keymap.set('n', '<C-k>', '<C-w>k', opts)
vim.keymap.set('n', '<C-l>', '<C-w>l', opts)

-- Resize with arrows
-- delta: 2 lines
vim.keymap.set('n', '<C-Up>', ':resize -2<CR>', opts)
vim.keymap.set('n', '<C-Down>', ':resize +2<CR>', opts)
vim.keymap.set('n', '<C-Left>', ':vertical resize -2<CR>', opts)
vim.keymap.set('n', '<C-Right>', ':vertical resize +2<CR>', opts)

-----------------
-- Visual mode --
-----------------

-- Hint: start visual mode with the same area as the previous area and the same mode
vim.keymap.set('v', '<', '<gv', opts)
vim.keymap.set('v', '>', '>gv', opts)
```

然后在 `init.lua` 文件里面再次加上一行导入这个文件

```Lua
...
require('keymaps') 

```

> 💡 `...` 表示省略了其他部分的代码

#### 安装插件管理器

一个强大的 `Nvim` 离不开插件的支持。我选用的是当下最为流行 [lazy.nvim](https://github.com/folke/lazy.nvim)。它支持如下许多特性：

-   正确处理不同插件之间的依赖
-   支持定制 Lazy loading，比如基于 Event、Filetype 等
-   …

新建 `~/.config/nvim/lua/plugins.lua` 文件并放入如下内容。下面的模板只完成了 `lazy.nvim` 自身的安装，**还没有指定其他第三方插件**

```Lua
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", -- latest stable release
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

require("lazy").setup({})

```

> 💡 在 `lazy.nvim` 指定第三方插件很简单，只需要在 `require("lazy").setup({ ... })` 的 `...` 里面声明插件

然后在 `init.lua` 文件里面再次加上一行导入这个文件

```Lua
...
require('plugins') 

```

此时你重启 `Nvim` 会发现黑屏没显示，这是因为 `lazy.nvim` 在安装自己，静待片刻即可☕️。等待 Dashboard 出现之后，可以输入 `:Lazy` 试试，如果看到了弹出了 `lazy.nvim` 的窗口，那就安装成功了🎉

技巧

_Tip_：用 `:q` 退出 `lazy.nvim` 的窗口

#### 主题配置

我安装的主题是 [nightfox系列](https://github.com/EdenEast/nightfox.nvim)，在 `plugins.lua` 进行修改

```Lua
...
require("lazy").setup({
    "EdenEast/nightfox.nvim",
})

```

保存更改并_重启_就可以看到 `lazy.nvim` 在帮我们安装插件了，新建并编辑 `~/.config/nvim/lua/colorscheme.lua` 文件

```Lua
-- define your colorscheme here
local colorscheme = 'nightfox'

local is_ok, _ = pcall(vim.cmd, "colorscheme " .. colorscheme)
if not is_ok then
    vim.notify('colorscheme ' .. colorscheme .. ' not found!')
    return
end

```

这里用到的 `pcall` 是 Lua 里面的 protected call，它会返回一个 `bool` 变量表示是否执行成功（跟 Go 语言的 `err` 功能类似）。这里采用 `pcall` 而不是直接在 `init.lua` 文件里面加上 `vim.cmd('colorscheme nightfox')` 是为了避免主题没有安装的情况下打开 `Nvim` 看到一大堆报错信息<sup id="fnref:2"><a href="https://martinlwx.github.io/zh-cn/config-neovim-from-scratch/#fn:2" role="doc-noteref">2</a></sup>

最后在 `init.lua` 文件里面导入就行

```Lua
...
require('colorscheme')

```
#### 自动补全指南

自己手动配置自动补全功能的话会比较繁琐，所以我们最好是借助于一些插件来帮忙，下面我讲讲**我摸索出来的最简单配置方法**

首先，第一个要用到的插件是 [nvim-cmp](https://github.com/hrsh7th/nvim-cmp)，它可以管理各种补全候选项来源，然后展示在补全菜单里面，还支持我们对外观等进行定制化。

我们先新建 `~/.config/nvim/lua/config/nvim-cmp.lua` 文件配置 `nvim-cmp`

```Lua

local has_words_before = function()
    unpack = unpack or table.unpack
    local line, col = unpack(vim.api.nvim_win_get_cursor(0))
    return col ~= 0 and vim.api.nvim_buf_get_lines(0, line - 1, line, true)[1]:sub(col, col):match("%s") == nil
end

local luasnip = require("luasnip")
local cmp = require("cmp")

cmp.setup({
    snippet = {
        -- REQUIRED - you must specify a snippet engine
        expand = function(args)
            require('luasnip').lsp_expand(args.body) -- For `luasnip` users.
        end,
    },
    mapping = cmp.mapping.preset.insert({
        -- Use <C-b/f> to scroll the docs
        ['<C-b>'] = cmp.mapping.scroll_docs( -4),
        ['<C-f>'] = cmp.mapping.scroll_docs(4),
        -- Use <C-k/j> to switch in items
        ['<C-k>'] = cmp.mapping.select_prev_item(),
        ['<C-j>'] = cmp.mapping.select_next_item(),
        -- Use <CR>(Enter) to confirm selection
        -- Accept currently selected item. Set `select` to `false` to only confirm explicitly selected items.
        ['<CR>'] = cmp.mapping.confirm({ select = true }),

        -- A super tab
        -- sourc: https://github.com/hrsh7th/nvim-cmp/wiki/Example-mappings#luasnip
        ["<Tab>"] = cmp.mapping(function(fallback)
            -- Hint: if the completion menu is visible select next one
            if cmp.visible() then
                cmp.select_next_item()
            elseif has_words_before() then
                cmp.complete()
            else
                fallback()
            end
        end, { "i", "s" }), -- i - insert mode; s - select mode
        ["<S-Tab>"] = cmp.mapping(function(fallback)
            if cmp.visible() then
                cmp.select_prev_item()
            elseif luasnip.jumpable( -1) then
                luasnip.jump( -1)
            else
                fallback()
            end
        end, { "i", "s" }),
    }),

  -- Let's configure the item's appearance
  -- source: https://github.com/hrsh7th/nvim-cmp/wiki/Menu-Appearance
  formatting = {
      -- Set order from left to right
      -- kind: single letter indicating the type of completion
      -- abbr: abbreviation of "word"; when not empty it is used in the menu instead of "word"
      -- menu: extra text for the popup menu, displayed after "word" or "abbr"
      fields = { 'abbr', 'menu' },

      -- customize the appearance of the completion menu
      format = function(entry, vim_item)
          vim_item.menu = ({
              nvim_lsp = '[Lsp]',
              luasnip = '[Luasnip]',
              buffer = '[File]',
              path = '[Path]',
          })[entry.source.name]
          return vim_item
      end,
  },

  -- Set source precedence
  sources = cmp.config.sources({
    { name = 'nvim_lsp' },    -- For nvim-lsp
    { name = 'luasnip' },     -- For luasnip user
    { name = 'buffer' },      -- For buffer word completion
    { name = 'path' },        -- For path completion
  })
})

```

然后我们修改 `plugins.lua` 文件添加相关的插件

```Lua
...
require("lazy").setup({
	-- Vscode-like pictograms
	{
		"onsails/lspkind.nvim",
		event = { "VimEnter" },
	},
	-- Auto-completion engine
	{
		"hrsh7th/nvim-cmp",
		dependencies = {
			"lspkind.nvim",
			"hrsh7th/cmp-nvim-lsp", -- lsp auto-completion
			"hrsh7th/cmp-buffer", -- buffer auto-completion
			"hrsh7th/cmp-path", -- path auto-completion
			"hrsh7th/cmp-cmdline", -- cmdline auto-completion
		},
		config = function()
			require("config.nvim-cmp")
		end,
	},
	-- Code snippet engine
	{
		"L3MON4D3/LuaSnip",
		version = "v2.*",
	},
    ...
})
```


**解释如下**

-   `cmp.setup` 函数的参数是一个 Lua 的 Table，用于设置各个选项（下面会解释）。后面你会发现很多第三方插件都用 `setup` 传入一个 Lua table 的方式进行配置，**这个是 `Nvim` 的 Lua 插件的惯例**
-   `LuaSnip` 是 code snippet 引擎，因为`nvim-cmp` 要求我们必须指定**至少一个** code snippet 引擎来源。你暂时用不到当它不存在也没有关系
-   `lazy.nvim` 支持用 `config = function() ... end` 指定对应插件被加载之后要运行的代码，这里我们是让 Lua 导入前面写好的 `nvim-cmp.lua` 配置文件
-   不要被上面的这么多插件吓到，`nvim-cmp` 为主，其他 `cmp-...` 的插件是用于在候选项来源和 `nvim-cmp` 之间交互，`lspkind.nvim` 是美化用的

##### nvim-cmp 里面的按键映射

按键映射用的是 `mapping = ...` ，每个按键绑定的格式是 `['<key-binding>'] = cmp.mapping.xxx,`，不同的 `cmp.mapping.xxx` 的含义可以用 `:h` 查看。**如果你想要用其他的按键，只要修改 `[...]` 里面的按键即可**

按照我的个人习惯，设置了

1.  `<C-k/j>` 或者 `<Tab>/<Shift-Tab>` 在各种候选项里面移动
2.  `<C-b/f>` 在候选项的文档里面移动
3.  `<CR>` 也就是回车键确定补全

##### nvim-cmp 里面的补全菜单

补全菜单的定制化用的是 `formatting = ...`

-   `fields` 字段规定了每个候选项要显示什么东西
-   `format = function(...)` 设置了不同的候选项的来源显示，在 `sources = ...` 里面声明来源

> 🎙️ 到这为止，重新启动 `Nvim` 后，等待插件安装完成后应该就能够用初步的自动补全功能了～

#### LSP

要把 `Nvim` 变成 IDE 就势必要借助于 LSP<sup id="fnref:3"><a href="https://martinlwx.github.io/zh-cn/config-neovim-from-scratch/#fn:3" role="doc-noteref">3</a></sup>，自己安装和配置 LSP 是比较繁琐的。不同的 LSP 安装方法不同，也不方便后续管理。[mason.nvim](https://github.com/williamboman/mason.nvim) 和配套的 [mason-lspconfig.nvim](https://github.com/williamboman/mason-lspconfig.nvim) 这两个插件很好解决了这个问题 🤗

首先修改 `plugins.lua` 文件，增加对应的插件

```Lua
...
require("lazy").setup({
	-- LSP manager
	"williamboman/mason.nvim",
	"williamboman/mason-lspconfig.nvim",
	"neovim/nvim-lspconfig",
    ...
})

```

新建一个 `~/.config/nvim/lua/lsp.lua` 文件并编辑，首先配置 `mason` 和 `mason-lspconfig`

```Lua
require('mason').setup({
    ui = {
        icons = {
            package_installed = "✓",
            package_pending = "➜",
            package_uninstalled = "✗"
        }
    }
})

require('mason-lspconfig').setup({
    -- A list of servers to automatically install if they're not already installed
    ensure_installed = { "pylsp", "lua_ls", "bashls", "rust_analyzer" },
})

```

> 💡 我们想要用什么语言的 LSP 就在 `ensure_installed` 里面加上，完整的列表可以看 [server\_configurations](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md)。我个人常用的就 `python/rust` 这两个编程语言，而因为我们都用 Lua 语言来配置 `Nvim`，所以也加上了 `lua_ls`

配置好 `mason-lspconfig` 之后，接下来就可以配置 `nvim-lspconfig` 了。因为配置的代码比较长，下面只展示了 `pylsp` 的配置，其他语言的配置大同小异。如果有疑惑，可以查看该文件的[最新版本](https://github.com/MartinLwx/dotfiles/blob/main/nvim/lua/lsp.lua)

> 💡 每个 LSP 都存在自己可以配置的选项，你可以自己去对应 LSP 的 GitHub 仓库查阅更多信息。如果要用默认配置的话，基本上每一个新的语言都只需要设置 `on_attach = on_attach`

编辑 `~/.config/nvim/lua/lsp.lua` 文件新增如下内容

```Lua
...
-- Set different settings for different languages' LSP
-- LSP list: https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md
-- How to use setup({}): https://github.com/neovim/nvim-lspconfig/wiki/Understanding-setup-%7B%7D
--     - the settings table is sent to the LSP
--     - on_attach: a lua callback function to run after LSP atteches to a given buffer
local lspconfig = require('lspconfig')

-- Customized on_attach function
-- See `:help vim.diagnostic.*` for documentation on any of the below functions
local opts = { noremap = true, silent = true }
vim.keymap.set('n', '<space>e', vim.diagnostic.open_float, opts)
vim.keymap.set('n', '[d', vim.diagnostic.goto_prev, opts)
vim.keymap.set('n', ']d', vim.diagnostic.goto_next, opts)
vim.keymap.set('n', '<space>q', vim.diagnostic.setloclist, opts)

-- Use an on_attach function to only map the following keys
-- after the language server attaches to the current buffer
local on_attach = function(client, bufnr)
    -- Enable completion triggered by <c-x><c-o>
    vim.api.nvim_buf_set_option(bufnr, 'omnifunc', 'v:lua.vim.lsp.omnifunc')

    -- See `:help vim.lsp.*` for documentation on any of the below functions
    local bufopts = { noremap = true, silent = true, buffer = bufnr }
    vim.keymap.set('n', 'gD', vim.lsp.buf.declaration, bufopts)
    vim.keymap.set('n', 'gd', vim.lsp.buf.definition, bufopts)
    vim.keymap.set('n', 'K', vim.lsp.buf.hover, bufopts)
    vim.keymap.set('n', 'gi', vim.lsp.buf.implementation, bufopts)
    vim.keymap.set('n', '<C-k>', vim.lsp.buf.signature_help, bufopts)
    vim.keymap.set('n', '<space>wa', vim.lsp.buf.add_workspace_folder, bufopts)
    vim.keymap.set('n', '<space>wr', vim.lsp.buf.remove_workspace_folder, bufopts)
    vim.keymap.set('n', '<space>wl', function()
        print(vim.inspect(vim.lsp.buf.list_workspace_folders()))
    end, bufopts)
    vim.keymap.set('n', '<space>D', vim.lsp.buf.type_definition, bufopts)
    vim.keymap.set('n', '<space>rn', vim.lsp.buf.rename, bufopts)
    vim.keymap.set('n', '<space>ca', vim.lsp.buf.code_action, bufopts)
    vim.keymap.set('n', 'gr', vim.lsp.buf.references, bufopts)
    vim.keymap.set("n", "<space>f", function()
        vim.lsp.buf.format({ async = true })
    end, bufopts)
end

-- Configure each language
-- How to add LSP for a specific language?
-- 1. use `:Mason` to install corresponding LSP
-- 2. add configuration below
lspconfig.pylsp.setup({
	on_attach = on_attach,
})

lspconfig.gopls.setup({
	on_attach = on_attach,
})

lspconfig.lua_ls.setup({
	on_attach = on_attach,
	settings = {
		Lua = {
			runtime = {
				-- Tell the language server which version of Lua you're using (most likely LuaJIT in the case of Neovim)
				version = "LuaJIT",
			},
			diagnostics = {
				-- Get the language server to recognize the `vim` global
				globals = { "vim" },
			},
			workspace = {
				-- Make the server aware of Neovim runtime files
				library = vim.api.nvim_get_runtime_file("", true),
			},
			-- Do not send telemetry data containing a randomized but unique identifier
			telemetry = {
				enable = false,
			},
		},
	},
})

lspconfig.bashls.setup({})

-- source: https://rust-analyzer.github.io/manual.html#nvim-lsp
lspconfig.rust_analyzer.setup({
	on_attach = on_attach,
	settings = {
		["rust-analyzer"] = {
			inlayHints = {
				-- Whether to show inlay hints after a closing } to indicate what item it belongs to.
				closingBraceHints = true,
			},
			checkOnSave = {
				allTargets = false,
			},
		},
	},
})
```

上面的按键绑定的意思是很直观的，这里就不多解释啦

最后在 `init.lua` 文件里面加上

```Lua
...
require('lsp')

```

重启 `Nvim` 之后，你应该可以在下面的状态栏看到 `Mason` 正在下载并安装前面我们指定的 LSP（**注意此时不能关闭 `Nvim`**），可以输入 `:Mason` 查看安装进度。在你等待安装的过程中，可以输入 `g?` 查看更多帮助信息了解如何使用 `mason` 插件

大功告成🎉🎉🎉


### 扩展

#### vi, vim 替换为 nvim

在 `.zshrc` 文件中增加以下内容

```bash
alias vi="nvim"  
alias vim="nvim"
```

#### 复制到系统剪切板

neovim和系统剪贴板的交互方式和vim的机制是不同的，需要外部的程序与系统剪贴板进行交互，可以在打开neovim时使用命令模式输入这个命令：`:help clipboard`

列出的是按优先级顺序进行选择交互的外部程序：

```bash
The presence of a working clipboard tool implicitly enables the '+' and '*'
registers. Nvim looks for these clipboard tools, in order of priority:

  - |g:clipboard|
  - pbcopy, pbpaste (macOS)
  - wl-copy, wl-paste (if $WAYLAND_DISPLAY is set)
  - waycopy, waypaste (if $WAYLAND_DISPLAY is set)
  - xclip (if $DISPLAY is set)
  - xsel (if $DISPLAY is set)
  - lemonade (for SSH) https://github.com/pocke/lemonade
  - doitclient (for SSH) https://www.chiark.greenend.org.uk/~sgtatham/doit/
  - win32yank (Windows)
  - termux (via termux-clipboard-set, termux-clipboard-set)
  - tmux (if $TMUX is set)


```

由于我是用的是Linux，所以直接安装 `xclip` 即可。

### 参考文章

- [从零开始配置 Neovim(Nvim)](https://martinlwx.github.io/zh-cn/config-neovim-from-scratch/)
- [Installing-Neovim](https://github.com/neovim/neovim/wiki/Installing-Neovim)
- [Adding a colorscheme/theme](https://www.youtube.com/watch?v=Ow03haHO1NE&list=RDCMUCS97tchJDq17Qms3cux8wcA&index=4)
- [Language Server Protocol - Wiki](https://en.wikipedia.org/wiki/Language_Server_Protocol)

