## anchors
- `^pattern` - pattern at beginning of line
- `pattern$` - pattern at end of line

## repetition
- `*` 0 or more
- `+` 1 or more
- `pattern{x}` exactly x times
- `pattern{x,y}` min of x times, max of y times
## groups
- `(?)` and `(?:)` - group and capture/don't capture (0+ times)
- `!` - negative lookahead (aka negation)

`^!(?!pattern).*` aka pattern is NOT at beginning of line
- `^` beginning of line anchor
- OPTIONAL? `(?:` group, but
	- `!` negate the following
		- `(..)` group (will be negated)
			- `?` look ahead and see if
				- `!pattern` pattern NOT matches
- `.*` followed by 0 or more of anything else

## spaces
- Prefer a real space e.g., ` ` ` *` ` +` ` {2,4}`
- modern regex engines can uses `\s`