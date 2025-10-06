
<font color="#1E90FF">VISUAL</font> 
<font color="#dc143c">INSERT</font>
<font color="#2E8B57">NORMAL</font>
<font color="#ffd700">COMMAND</font>


## wildmenu
`:help wildmenu`
<font color="#ffd700">COMMAND</font> mode autocomplete
-  <font color="#EE82EE">NOTE: Enter</font> will accept the suggest AND execute it 
- `<TAB>` triggers autocomplete prompt
- `<CTRL+N`>/ `<CTRL+P`> next/prev autocomp suggestion
- `<CTRL+E>` end completion WITHOUT accepting suggestion
- `<CTRL+Y>` accept suggestion

Arrows are weird, `<Left>` / `<Right>` also select suggestions, up and down DO NOT

In the menu completion
- `<CR>` move into submenu
- `<Up>` / `<Down>` move directories

`CTRL+Y` is DEFAULT for accepting autocomplete
- suggestion also `<C-Enter>`

`CTRL+w d`  in <font color="#2E8B57">NORMAL</font> mode shows diagnostics (error/warning) for line

`ZZ` (aka `SHIFT+Z SHIFT+Z`) closes popup from these commands

Global default keymaps in nvim
- `gra` is mapped in <font color="#2E8B57">NORMAL</font>  and <font color="#1E90FF">VISUAL</font>  mode to vim.lsp.buf.code_action()
- `gri` is mapped in <font color="#2E8B57">NORMAL</font> mode to vim.lsp.buf.implementation()
- `grn` is mapped in <font color="#2E8B57">NORMAL</font> mode to vim.lsp.buf.rename()
- `grr` is mapped in <font color="#2E8B57">NORMAL</font> mode to vim.lsp.buf.references()
- `grt` is mapped in <font color="#2E8B57">NORMAL</font>  mode to vim.lsp.buf.type_definition()
- `gO` is mapped in <font color="#2E8B57">NORMAL</font> mode to vim.lsp.buf.document_symbol()
- `CTRL-S` is mapped in <font color="#dc143c">INSERT</font> mode to vim.lsp.buf.signature_help()
- `an` and `in` are mapped in <font color="#1E90FF">VISUAL</font>  mode to outer and inner incremental selections, respectively, using vim.lsp.buf.selection_range()
- -----------------------------------------------
- `K` is "hover", shows function definitions 

Buffer-local default keymaps in nvim
- omnifunc is set to vim.lsp.omnifunc()
	- `CTRL+X CTRL-O` does autocomplete in <font color="#dc143c">INSERT</font> mode
- tagfunc is set to vim.lsp.tagfunc()
	- `CTRL+]` - is mapped in <font color="#2E8B57">NORMAL</font> mode to vim.lsp.buf.definition() 
		- USES CURRENT WINDOW
		- pushes original window to tagstack
		- `CTRL+T` pops previous tag from tag stack, and sets window to it
	- `CTRL+W CTRL+]` is mapped in <font color="#2E8B57">NORMAL</font> mode to vim.lsp.buf.definition() 
		- IN SPLIT WINDOW (like custom `gd`)
		- tag stack available in split window
- - -----------------------------------------------
- `Ctrl+S` shows signature help for current symbol

Custom/Manual keymaps
- `gd` -  is mapped to vim.lsp.buf.definition() and customized to be a Vsplit
- `gD` - is mapped to vim.lsp.buf.declaration() and customized to be a Vsplit


`:lua vim.lsp.buf.workspace_symbol()`
- prompts for symbol
- searches workspace even if not open in buffer (or included in header)

`:sp` == `CTRL+W CTRL+S`
`:vsp` == `CTRL+W CTRL+V`
`:q` == `CTRL+W q`

# File explorer
`:30 Lexplore` - % of screen to show filemanager
- `enter` opens file in new buffer

# Commands
`q` - close window/pane
`bd` - unload buffer if no changes
	- `bw` buffer wipeout