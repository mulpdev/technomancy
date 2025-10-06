https://jqlang.org/

jq is like `sed` for JSON data - you can use it to slice and filter and map and transform structured data with the same ease that `sed`, `awk`, `grep` and friends let you play with text.

https://github.com/jqlang/jq/releases/download/jq-1.8.1/jq-linux-amd64

```sh
org=bergercookie repo=asm-lsp ; curl https://api.github.com/repos/$org/$repo/releases/latest -s | jq .name -r
```
