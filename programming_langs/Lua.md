# Strings

```lua
foo = "Foo"
bar = "bar"
mystr = "Hello World " .. foo .. "/" .. bar
```

# write file

```lua
file = io.open("example.txt", "w")
file:write("Hello, World!")
file:close() -- REQUIRED TO FLUSH BUFFER
```

```lua
print(vim.api.nvim_get_current_win())  --print number
print(#vim.api.nvim_get_current_win()) --print length of object
```