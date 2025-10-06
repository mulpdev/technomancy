tmux `prefix` == screen `meta-key`
# tmux
- default prefix is `ctrl+b`
- tmux inside tmux is `<prefix> <prefix> key`

`<prefix> d` disconnect w/o quit
`<prefix> <arrow>` move pane arrow
`<prefix> z` toggle fullscreen pane
`<prefix> $` rename current tmux session
`<prefix> :` command mode (bottom)

`set -g mouse on`

`split-window` (poorly named?) splits the target pane based on args
```
full window height split (must have 2 horizontal panes already)
1|3
2|

split-window -hf ("height full")
```

```
full window width split (must have 2 vertical panes already)

split-window -vf ("Vidth height, not vertical)
```

## Dump scrollback to file
Ensure your scrollback is long enough to get where you want 
- use .tmux.conf or set it prior to doing the thing you want to capture

`<prefix> :` `set -g mouse on`
- check scrollback size in upper right corner

Save scrollback to buffer 
`<prefix> :` `capture-pane -S -1234` where 1234 is number of lines

Save buffer to file
`<prefix> :` `save-buffer filename.txt`

# Screen
Default meta-key is `ctrl+a`

`<meta-key> K` - kill screen session
`<meta-key> [` -  enable scrollback mode. Use PgUp/PgDn/mouse
- `esc` key ends scroll-back mode
- PgUp/PgDown move exactly the height of the window
- When copying large chunks of the buffer, position any text to copy/paste so the first line is the first thing to copy. The cursor will be on the bottom row

## Serial connections
`screen /dev/ttyUSB0 <baudrate>`

``