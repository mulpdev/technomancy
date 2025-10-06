# lua snippets

```lua
:lua print(...)  -- print number
:lua print(#...) -- print length of object
:lua vim.print(...) -- print table
-- ; separates lines
```

Examples
```lua
:lua vim.print(vim.opt.packpath) -- ".opt" (WRONG) prints address of table
:lua vim.print(vim.o.packpath)   -- ".o" print packpath all on one line
:lua vim.tbl_map(print, vim.opt.packpath:get()) -- print packpath, 1 per line

-- write markdown links for lazy plugins
:lua file = io.open(os.getenv("HOME") .. "/code/test/lazyplugins.txt", "w"); for _, plugin in pairs(require('lazy').plugins()) do file:write("[" .. plugin.name .. "](" .. plugin.url .. ")\n") end; file:close()   

```


# wildmenu
Command mode auto completion
https://stackoverflow.com/a/79339240
`:help wildmenu`
```
If you want <Left> and <Right> to move the cursor instead of selecting a different match, use this:                               cnoremap <Left> <Space><BS><Left>                                cnoremap <Right> <Space><BS><Right>
```

https://www.reddit.com/r/neovim/comments/r5579w/cj_struggle_with_wildmenu/
Remap `<CTRL+J>` to `<CTRL+N>`
```
cnoremap <expr><C-j> wildmenumode() ? "\<C-n>" : "\<C-j>"
```

https://www.reddit.com/r/neovim/comments/r5579w/comment/hmmtiem/
If using a nvim-cmp / floating window instead of wildmenu
```
cmap <expr> <c-j> luaeval('require"cmp".visible()') ? "\<C-n>" : "\<C-j>"
```
Manual keymaps
```lua
vim.keymap.set("n", "gd", function()
	vim.cmd("vsplit") -- | vertical resize -10 to make window smaller
	vim.lsp.buf.definition()
end, opts)

vim.keymap.set("n", "gD", function()
	vim.cmd("vsplit") -- | vertical resize -10 to make window smaller
	vim.lsp.buf.declaration()
end, opts)

```

# keymaps diagnostic
https://neovim.io/doc/user/diagnostic.html#diagnostic-defaults

These diagnostic keymaps are created unconditionally when Nvim starts:
- `]d` jumps to the next diagnostic in the buffer. 
- `[d` jumps to the previous diagnostic in the buffer.
- `]D` jumps to the last diagnostic in the buffer. 
- `[D` jumps to the first diagnostic in the buffer.
- `<C-w>d` shows diagnostic at cursor in a floating window. CTRL-W_d-default
# Lua
Lua API https://neovim.io/doc/user/lua.html

Printing
```lua
:lua print(vim.api.nvim_get_current_win()) -- print integer
:lua vim.print(vim.wo[vim.api.nvim_get_current_win()]) -- print table
```


https://stackoverflow.com/questions/73850771/how-to-get-all-of-the-properties-of-window-in-neovim
```lua
local all_options = api.nvim_get_all_options_info()
local win_number = api.nvim_get_current_win()
local v = vim.wo[win_number]
local all_options = api.nvim_get_all_options_info()
local result = ""
for key, val in pairs(all_options) do
    if val.global_local == false and val.scope == "win" then
        result = result .. "|" .. key .. "=" .. tostring(v[key] or "<not set>")
    end
end
print(result)
-- #############################
print(vim.wo[vim.api.nvim_get_current_win()]['previewwindow']) -- true or false

```


# Preview window
```lua
:set winhighlight=Normal:Normal,NormalNC:NormalNC

-- set existing highlight group key,value
:highlight NormalNC guibg=#FFFFFF
:highlight clear NormalNC

-- create existing highlight group key,value
:highlight q guibg=#FFFFFF
:set winhighlight=Normal:Normal
```


# Config Lua
some statements have IMPLICIT paths and ignore the ".lua" extension
```lua
require("config.options") -- ~/.config/{alias_nvim}/lua/config/options.lua
vim.lsp.enable('clangd') -- ~/.config/{alias_nvim}/lsp/clangd.lua
```


Some things like `.setup{...}` and `.config{...}` take objects/dicts of the config or description. These usually be substituted for a `return{...}` in a file elsewhere

```lua
require 'lspconfig'.clangd.setup 
vim.lsp.config.clangd = 
vim.lsp.config('clangd', 
{
	key1 = val1,
	key2 = {"val2a", "val2b"}
	key3 = {key3a = val3a, key3b = val3b}
}

-- original location
vim.lsp.enable('clangd')

-- new file lsp/clangd.lua
return {
	key1 = val1,
	key2 = {"val2a", "val2b"}
	key3 = {key3a = val3a, key3b = val3b}
}
```


# luarocks
```sh
luarocks --lua-version=5.1 --tree=$HOME/.config/native-nvim/rocks install tree-sitter-rust
```

# Build from source
Development moves fast, package managers are often way behind

```sh
# IF DEBIAN 13 also install: libnsl-dev (and maybe libnsl2)
sudo apt install ninja-build gettext cmake unzip curl
git clone neovim.git
cd neovim
mkdir build
make CMAKE_BUILD_TYPE=RelWithDebInfo
sudo make install
```

Avoid CMake cache errors when building
```sh
find .deps/ -name "CMakeCache.txt" -exec rm {} \;
```
# Run nvim with "profiles"
```sh
mkdir -p ~/.config/simple-nvim
mv init.vim ~/.config/simple-nvim
alias nvims='NVIM_APPNAME=simple-nvim nvim'
nvims # simple nvims
```

```sh
mkdir -p ~/.config/qwerty-nvim
alias nvimq='NVIM_APPNAME=qwerty-nvim nvim'
nvimq # qwerty nvims
```


# vim.lsp
see 
- https://neovim.io/doc/user/lsp.html#lsp-defaults
- `:help lsp-defaults`
- `:h ins-completion`
- https://github.com/neovim/nvim-lspconfig - default configs for vim.lsp builtin
## GLOBAL DEFAULTS
`gra gri grn grr grt i_CTRL-S v_an v_in` These GLOBAL keymaps are created unconditionally when Nvim starts:
- `gra` is mapped in Normal and Visual mode to vim.lsp.buf.code_action()
- `gri` is mapped in Normal mode to vim.lsp.buf.implementation()
- `grn` is mapped in Normal mode to vim.lsp.buf.rename()
- `grr` is mapped in Normal mode to vim.lsp.buf.references()
- `grt` is mapped in Normal mode to vim.lsp.buf.type_definition()
- `gO` is mapped in Normal mode to vim.lsp.buf.document_symbol()
- `CTRL-S` is mapped in Insert mode to vim.lsp.buf.signature_help()
- `an` and `in` are mapped in Visual mode to outer and inner incremental selections, respectively, using vim.lsp.buf.selection_range()

## BUFFER-LOCAL DEFAULTS
- `omnifunc` is set to vim.lsp.omnifunc(), use i_CTRL-X_CTRL-O to trigger completion.
- `tagfunc` is set to vim.lsp.tagfunc(). This enables features like go-to-definition, :tjump, and keymaps like CTRL-], CTRL-W_], CTRL-W_} to utilize the language server.
- `formatexpr` is set to vim.lsp.formatexpr(), so you can format lines via gq if the language server supports it.
	- To opt out of this use gw instead of gq, or clear 'formatexpr' on LspAttach.
- `K` is mapped to vim.lsp.buf.hover() unless 'keywordprg' is customized or a custom keymap for K exists.
- Document colors are enabled for highlighting color references in a document.
	- To opt out call vim.lsp.document_color.enable(false, args.buf) on LspAttach.

# Plugins

nvim-treesitter - better code parsing and color highlighting

NO PLUGIN MANAGER
in a packpath dir 
- nvim-lspconfig uses ~/.config/nvim/pack/{alias_nvim}/start/nvim-lspconfig
```
:set packpath?
:lua vim.tbl_map(print, vim.opt.packpath:get())
```

```
:lua vim.tbl_map(print, vim.opt.packpath:get())
```


```
pack
└── vendor
    ├── opt
    │   └── package-one
    └── start
        ├── package-two
        └── package-three
```

```sh
cd ~/.config/nvim/
mkdir -p pack/nvim/start
git clone <nvim-package> ~/.config/nvim/pack/nvim/start/
```

or automagically
```lua
vim.pack.add{
  { src = 'https://github.com/neovim/nvim-lspconfig' },
}
```

LuaSnip depends on jsregexp
```sh
sudo apt install liblua5.1-0-dev
cd ~/.config/{alias-nvim}/pack/nvim/start/jsregexp
make
```
# Lua

```lua
:lua vim.tbl_map(print, vim.opt.packpath:get())

:lua file = io.open(os.getenv("HOME") .. "/packpath.txt", "w"); vim.tbl_map(file:write, vim.opt.packpath:get())
```

Write string to file
```lua
local file = io.open(os.getenv("HOME") .. "/lazyvim_plugins.txt", "w")
file:write(plugin_str)
file:close()
```


# vim.diagnostic.config
```lua
vim.diagnostic.config({
  virtual_text = true,  -- Show diagnostic messages as virtual text (default)
  signs = true,         -- Show diagnostic signs in the sign column
  underline = true,     -- Underline problematic code
  update_in_insert = false, -- Don't update diagnostics in insert mode
  severity_sort = true, -- Sort diagnostics by severity
  float = {             -- Options for floating window diagnostics
    border = "rounded",
    source = "always",
    header = "",
    prefix = "",
  },
})

```

# LazyVim
## Links

https://neovim.discourse.group/t/cannot-figure-out-how-to-configure-lsp-with-lazy/4462/3

https://www.lazyvim.org/configuration/examples

https://www.lazyvim.org/plugins/lsp

https://www.reddit.com/r/neovim/comments/18hzcok/most_straightforward_way_to_setup_lsp/
- https://www.reddit.com/r/neovim/comments/18hzcok/comment/kdb40dv/
	- https://github.com/SwayKh/dotfiles/blob/main/nvim/lua/plugins/lsp.lua

https://blog.viktomas.com/graph/neovim-lsp-rename-normal-mode-keymaps/

https://stackoverflow.com/questions/597687/how-to-quickly-change-variable-names-in-vim

https://medium.com/@shaikzahid0713/code-completion-for-neovim-6127401ebec2

https://www.reddit.com/r/neovim/comments/18hrno5/automatic_lsp_rename_during_regular_editing_how/

http://www.lazyvim.org/extras/editor/refactoring

https://www.reddit.com/r/vim/comments/1zvks1/best_way_to_rename_variable_in_several_spots_in_a/

https://stackoverflow.com/questions/79302523/unable-to-use-rename-in-neovim-with-ruby-lsp

https://www.reddit.com/r/neovim/comments/19054s4/help_how_do_i_auto_complete_with_tab_in_lazyvim/
`Ctrl+E` - Opens File Manager

Plugins stored here
```sh
~/.local/share/nvim/
```


## LazyVim Configuration

repos
- https://github.com/folke/lazy.nvim

```
├── init.lua
└── lua
    ├── config
    │   ├── autocmds.lua
    │   ├── keymaps.lua
    │   ├── lazy.lua
    │   ├── lsp_config.lua
    │   └── options.lua
    └── plugins
        ├── blink-cmp.lua
        ├── indent.lua
        ├── lsp.lua
        ├── lualine.lua
        ├── no-config.lua
        ├── telescope.lua
        └── treesitter.lua

```

- `init.lua` - "bootstrap lazy.nvim, LazyVim, and plugins"
- `lua/config/` autocmds, keymaps, options are auto loaded. lazy.lua is from bootstrap above
	- `autocmds.lua` - nothing
	- `keymaps.lua` - nothing
	- `lazy.lua`
		- https://github.com/LazyVim/starter/blob/main/lua/config/lazy.lua 
		- Modified for offline git
	- `lua/config/options.lua` vim options
- `lua/plugins/` plugins (aka "spec") setup
	- docs: http://www.lazyvim.org/configuration/plugins
	- example: https://github.com/LazyVim/starter/blob/main/lua/plugins/example.lua
	-  tl;dr if you need to modify the defaults, make a file in here. The name doesn't technically matter but convention seems to be the plugin name?
		```lua
		return {
			"repopath/reponame" -- combined with lazy setup git: {"url_format"}
			-- setup here 
			--   assign variables here
			--   Options/Full Spec ex: http://www.lazyvim.org/plugins/lsp
		}
		```

## Offline Git
Create globals inside `~/.config/nvim/lua/config/lazy.lua`

```lua
-- Line 1
-- COMBINE with "https://" .. GIT_DOMAIN
-- COMBINE with "git@" .. GIT_DOMAIN
GIT_DOMAIN = "github.com"

-- MODIFIED FOR OFFLINE INSTALL
local lazyrepo = "https://" .. GIT_DOMAIN .. "/folke/lazy.nvim.git"
if string.sub(lazyrepo, 1, 19) == "https://github.com" then
	local out = vim.fn.system({ "git", "clone", "--filter=blob:none", "--branch=stable", lazyrepo, lazypath })
else
	local out = vim.fn.system({ "git", "clone", "--filter=blob:none", lazyrepo, lazypath })
  end
-- END MODIFIED
```

Create globals inside `~/.config/nvim/lua/plugin/FILENAME.lua`

```
-- stylua: ignore
if true then return {} end
```

```
-- MODIFIED. Extras
    { import = "lazyvim.plugins.extras.lang.python" },

```

