These are <font color="#FF1493">KEYPRESSES</font> and these instructions are CUMULATIVE keypresses

Note: clauses below can be negated by prepending `!`

## BEFORE SEARCH, set case sensitivity if needed
`-I` toggle full case insensitive
`-i` toggle case insensitive, UNLESS capital letter in pattern

## start a search by pressing
`/` - show all lines and highlight pattern
`&/` - only show matching lines of subsequent pattern
`!&/` - only show NON-matching lines of subsequent pattern
?? `/&` with nothing else removes the filter

## IMMEDIATELY disable regex if required
`ctrl+R`
- of you will escape the pattern with character classes e.g., `/*` becomes `[/][*]` (or full keystroke `/[/][*]` )

## patterns (default is regex on)
`pattern` - ANY occurrence of the pattern
`\<pateern\>` - whole word occurrence of the pattern. put `\<` immediately before pattern and `/>` immediately after

# navigation
`n` - next
`N` - previous