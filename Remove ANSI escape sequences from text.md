
https://web.archive.org/web/20210226122732/ascii-table.com/ansi-escape-sequences.php
![[ansi-escape-seq.png]]
## for graphics mode escape sequences
modified from: https://superuser.com/questions/380772/removing-ansi-color-codes-from-text-stream

```perl
sed -e 's/\x1b\[[0-9;]*m//g'
```
- ESC == 0x1b
- color bytes are numeric
- 'm' (0x6d) is final byte


