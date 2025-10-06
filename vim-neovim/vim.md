`*yy` and `*p` yanks and paste to system clipboard IF compiled with "+clipboard" (`:echo has('clipboard')` )


# keymaps and commands

`CTRL+w |` fullscreen window vertically
`CTRL+w _` fullscreen window horizontally
`CTRL+w =` equal sized windows based on splits 

`CTRL+6` `CTRL ^` Edit "alternate" file. alternate mains previous edited

"fullscreen a tab" aka split tab into new window (doesn't modify existing split) and then close it
```
:tab split
:tabc

:noremap tt :tab split<CR>
```


# show key maps
- `:map`: Displays all currently active keymaps across all modes (normal, visual, select, operator-pending, insert, command-line).
- `:nmap`: Shows keymaps specific to normal mode.
- `:vmap`: Shows keymaps specific to visual mode.
- `:imap`: Shows keymaps specific to insert mode.
- Other mode-specific commands include `:smap` (select mode), `:omap` (operator-pending mode), and `:cmap` (command-line mode).
- `:verbose map {key}`: Shows a specific keymap and where it was defined. Replace `{key}` with the key sequence you want to check (e.g., `:verbose map <leader>ff`).

https://vi.stackexchange.com/a/38860
There are two modes often confused as one:
- `:h Visual-mode`
- `:h Select-mode`

The indicator or prefix used in keymaps for these modes are:
- `:h mapmode-x`: activated only in visual mode
- `:h mapmode-s`: activated only in select mode
- `:h mapmode-v`: activated in both visual and select mode

The difference between select-mode and visual-mode can be found in `:h Select-mode`. As for the keymap distinction, vim suggests the following in `:h mapmode-x`:

> NOTE: Mapping a printable character in Select mode [`mapmode-v`] may confuse the user. It's better to explicitly use :xmap and :smap for printable characters.

**TL;DR**: When in doubt, use strictly `visual-mode` and `mapmode-x`.
# vim
`{actions}/{pattern}/{replace}/{modifiers}`

`{pattern}`
- if contains `\C` force case
- if contains `\c` ignore case

`{actions}
- `s` substitute
- `X,Ys` substitute on lines X-Y (inclusive)
- `%s` all lines in the file

`{modifiers}`
- `g` global, all occurrences on line
- `c` confirm replacement
- `I` case sensitive

I don't use `~/.vimrc` by default as of 20250915 to keep everything in a single folder for dotfiles repo

`.vim/`
- `vimrc` - no . in name
- `colors`
	- `{colorscheme}.vim` - can't just git clone, must copy the .vim files into this directory
- `pack/plugins/start/` - (Vim v8.0+) plugins folder. Clone repo into this folder to use vim's default plugin manager

# check variable
`:set optionname?`

# vimrc options
Options documentation: https://vimdoc.sourceforge.net/htmldoc/options.html

## locations
source https://learnvim.irian.to/customize/vimrc
```
The conventional wisdom for vimrc is to add a `.vimrc` dotfile in the root directory `~/.vimrc` (it might be different depending on your OS).

Behind the scene, Vim looks at multiple places for a vimrc file. Here are the places that Vim checks:

- `$VIMINIT`
- `$HOME/.vimrc`
- `$HOME/.vim/vimrc`
- `$EXINIT`
- `$HOME/.exrc`
- `$VIMRUNTIME/default.vim`

When you start Vim, it will check the above six locations in that order for a vimrc file. The first found vimrc file will be used and the rest is ignored.
```

# vimrc variables
source  
- https://stackoverflow.com/questions/2284556/how-do-i-use-variables-in-my-vimrc
- https://vi.stackexchange.com/questions/21392/how-do-i-use-a-variable-or-return-value-in-option-command-or-mapping
- https://learnvimscriptthehardway.stevelosh.com/chapters/19.html

Setting options using OG way
-  can combine set of multiple options into a single line
```
set tabstop=4 'shiftwidth=4 expandtab`
```

Setting options using a variable with let and execute
-  can combine set of multiple options into a single line
```
let tabsize=5
execute 'set tabstop='.tabsize 'shiftwidth='.tabsize 'expandtab'
```

Setting options using a variable with let
- Each option must be assigned on a separate line
```
let tabsize=6
let &tabstop=tabsize
let &shiftwidth=tabsize
```



# OG vimconfig
```
" ~/.config/nvim/init.vim

" Anytime a buffer read or creates a new file with the extension:
" .asm it will format it with the nasm file syntax
" .S it will format it with the asm (gas) file syntax
au BufRead,BufNewFile *.asm set filetype=nasm
au BufRead,BufNewFile *.S set filetype=asm

set tabstop=2
set softtabstop=2
set shiftwidth=2
set expandtab
set splitbelow
set splitright
set number
```

# Fixed vimrc
```
"------------------------------------------------------------------------------
"   tabstop     Number of spaces that a <Tab> in the file counts for
"   softtabstop Number of spaces that a <Tab> counts for while performing editing like <Tab> or <BS>
"   shiftwidth  Number of spaces to use for each step of (auto)indent. Used with << >> and cident 
"   expandtab   Number of spaces to insert a <Tab>. Use CTRL-V<Tab> for a real tab
let tabsize=2

" See under https://vimdoc.sourceforge.net/htmldoc/options.html#'tabstop'
"   4 ways main ways to use tabs in vim'
"
" 1.Always keep 'tabstop' at 8, set 'softtabstop' and 'shiftwidth' to 4
"          (or 3 or whatever you prefer) and use 'noexpandtab'.  Then Vim
"          will use a mix of tabs and spaces, but typing <Tab> and <BS> will
"          behave like a tab appears every 4 (or 3) characters.
"execute 'set tabstop=8' 'softtabstop='.tabsize 'shiftwidth='.tabsize 'noexpandtab'
" 2.Set 'tabstop' and 'shiftwidth' to whatever you prefer and use
"          'expandtab'.  This way you will always insert spaces.  The
"          formatting will never be messed up when 'tabstop' is changed.
execute 'set tabstop='.tabsize 'shiftwidth='.tabsize 'expandtab'
"3. Set 'tabstop' and 'shiftwidth' to whatever you prefer and use a
"          |modeline| to set these values when editing the file again.  Only
"          works when using Vim to edit the file.
"execute 'set tabstop='.tabsize 'shiftwidth='.tabsize
"4. Always set 'tabstop' and 'shiftwidth' to the same value, and
"          'noexpandtab'.  This should then work (for initial indents only)
"          for any tabstop setting that people use.  It might be nice to have
"          tabs after the first non-blank inserted as spaces if you do this
"          though.  Otherwise aligned comments will be wrong when 'tabstop' is
"          changed.
"execute 'set tabstop='.tabsize 'shiftwidth='.tabsize 'noexpandtab'

```