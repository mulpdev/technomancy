
# Theory 
Basic syntax
```
<number><verb><subject/direction>
```

Modifier
- Modify the subject (default is `shift`, e.g., make it caps) 



A "word" consists of alphanumeric and most punctuation
- underscore `_` is part of a word
- dash `-` is a word
```
change_me_1 # cw abc = abc
change-me-2 # cw abc = abc-me-2
```

Now with the modifier on "word" (the subject)
```
change_me_1 # cW abc = abc
change-me-2 # cW abc = abc

change_me-4() # cW abc = abc
```

`dt<char>` will delete up to the char
`df<char>` will delete including the char

`f/F` is 'find' (fwd/rev)
	`;` and `,` will go in current search direction


# Scoping
Does not work in nvim 0.4.3  unless in between ( )
Does work in nvim 0.9.1

```c
//input
void coolfunc(int arg1, int arg2) {
  ...
}
// mode: normal
// cursor anywhere on first line before )
// type: di( OR di)

//result
void coolfunc() {
  ...
}
```

# Multiline insert at same spot
go to column
ctrl+V for column based visual mode
shift+I for special insert (only will type on topmost line)
esc when done