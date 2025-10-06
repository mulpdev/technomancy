Get all apt install commands from history
```sh
# prints one per line
history | cut -c 8- | grep -E "^sudo apt install|^apt install" | while IFS='' read -r line; do echo "${line#*apt install}"; done | awk '{$1=$1;print}'

# all on one line, space delimited
cat h.txt | cut -c 8- | grep -E "^sudo apt install|^apt install" | while IFS='' read -r line; do printf "${line#*apt install}"; done | awk '{$1=$1;print}'  
libfuse2 chromium cmake ninja ninja-build ninja-build gettext cmake unzip curl libnsl libnsl2 libnsl-dev

# remove duplicated lines
history | cut -c 8- | grep -E "^sudo apt install|^apt install" | while IFS='' read -r line; do echo "${line#*apt install}"; done | awk '{$1=$1;print}' | tr ' ' '\n' | sort -u

# remove duplicated lines, print on one line space delimited
history | cut -c 8- | grep -E "^sudo apt install|^apt install" | while IFS='' read -r line; do echo "${line#*apt install}"; done | awk '{$1=$1;print}' | tr ' ' '\n' |  
sort -u | tr '\n' ' '
```

# Loop
```sh
while IFS= read -r line; do
    echo "Text read from file: $line"
done < my_filename.txt
```

```sh
cmd | while IFS='' read -r line; 
    do echo "${line#*apt install}"; 
done
```

```sh
cmd | while IFS='' read -r line; do echo "${line#*apt install}"; done
```

# strip leading and trailing whitespace
```sh
awk '{$1=$1;print}'

awk '{$1=$1};1'
```

# awk
```sh
awk '{if (match($11, "qemu")) print 2$}'
```

$0 is the full string
$1..n is a specific column

- `'` starts awk syntax string
	- `{` starts awk syntax
		- if
			- `(match($11,"qemu"))` if column 11 matches string "qemu"
		- then
			- `print $2` print the second column
	- `}` end awk syntax
- `'` end awk syntax string


# sed

```sh
$ echo "/home/user/Downloads/sites/blah/dsafj/gdbfd.ext" | sed 's+.*sites/++'  
blah/dsafj/gdbfd.ext
```

```sh
sed -e 's+.*sites/++;t;d' paths.txt
sed -e 's+.*sites/++' -e 't' -e 'd' paths.txt
```
sed
- `-e` script/expression  (multiple -e can be chained together on one line)
- `'` start of expression (pattern space?)
	- `s+.*sites/++` regex but with delimited changed to '+' to avoid conflicts with '/'
	- `;` scipt/expression delimiter? (instead of multiple -e, which is easier to read?)
	- `t` goto label ONLY IF a successful `s`ubstitutions since the last input line was read or conditional branch was taken
		- if no label, start next cycle
	- `d` delete pattern space, aka the line was not subbed (or matched?) and will be deleted/not printed

```sh
sed -e 's+.*sites/++' -e 'ta' -e 'd' -e ':a s,/*[^/]\+/*$,,' paths.txt
```

sed 
- `-e 's+.*sites/++'` regex but with delimited changed to '+' to avoid conflicts with '/'
- `-e 'ta'` jump to label a ONLY IF a successful `s`ubstitutions since the last input line was read or conditional branch was taken
-  `-e 'd'` delete pattern space, aka the line was not subbed (or matched?) and will be deleted/not printed
- `-e ':a s,/*[^/]\+/*$,,'` 
	- `:a` label a
	- `s,/*[^/]\+/*$,,'` strip last part of path: filename + extension

```sh
# remove filename and extension
sed -e 's=[^/]*$==' -e 't' -e 'd' paths.txt

# keep everything between .../sites(/blah/)...
sed -e 's=.*sites\(/.*/\).*=\1=' -e 't' -e 'd' paths1.txt | sort | uniq > paths3.txt

q=$(sed -e 's=\(^.*sites/.*/\).*$=\1=' -e 't' -e 'd' paths1.txt | sort | uniq)
cnt=0; echo "${q}" | while IFS='' read -r line; do echo "${cnt}: ${line}"; cnt=$((cnt+1)); done

# find only files in Downloads folder
q="$(sed -e 's=\(^.*Downloads/[^/]*\).*=\1=' -e 't' -e 'd' oct_recovery_files.txt | sort | uniq)"
```

https://unix.stackexchange.com/a/33005
There are two levels of interpretation here: the shell, and sed.

In the shell, everything between single quotes is interpreted literally, except for single quotes themselves. You can effectively have a single quote between single quotes by writing `'\''` (close single quote, one literal single quote, open single quote).

Sed uses [basic regular expressions](http://en.wikipedia.org/wiki/Regular_expression#POSIX_basic_and_extended). In a BRE, in order to have them treated literally, the characters `$.*[\^` need to be quoted by preceding them by a backslash, except inside character sets (`[…]`). Letters, digits and `(){}+?|` must not be quoted (you can get away with quoting some of these in some implementations). The sequences `\(`, `\)`, `\n`, and in some implementations `\{`, `\}`, `\+`, `\?`, `\|` and other backslash+alphanumerics have special meanings. You can get away with not quoting `$^` in some positions in some implementations.

Furthermore, you need a backslash before `/` if it is to appear in the regex outside of bracket expressions. You can choose an alternative character as the delimiter by writing, e.g., `s~/dir~/replacement~` or `\~/dir~p`; you'll need a backslash before the delimiter if you want to include it in the BRE. If you choose a character that has a special meaning in a BRE and you want to include it literally, you'll need three backslashes; I do not recommend this, as it may behave differently in some implementations.

In a nutshell, for `sed 's/…/…/'`:

- Write the regex between single quotes.
- Use `'\''` to end up with a single quote in the regex.
- Put a backslash before `$.*/[\]^` and only those characters (but not inside bracket expressions). (Technically you shouldn't put a backslash before `]` but I don't know of an implementation that treats `]` and `\]` differently outside of bracket expressions.)
- Inside a bracket expression, for `-` to be treated literally, make sure it is first or last (`[abc-]` or `[-abc]`, not `[a-bc]`).
- Inside a bracket expression, for `^` to be treated literally, make sure it is _not_ first (use `[abc^]`, not `[^abc]`).
- To include `]` in the list of characters matched by a bracket expression, make it the first character (or first after `^` for a negated set): `[]abc]` or `[^]abc]` (not `[abc]]` nor `[abc\]]`).

In the replacement text:

- `&` and `\` need to be quoted by preceding them by a backslash, as do the delimiter (usually `/`) and newlines.
- `\` followed by a digit has a special meaning. `\` followed by a letter has a special meaning (special characters) in some implementations, and `\` followed by some other character means `\c` or `c` depending on the implementation.
- With single quotes around the argument (`sed 's/…/…/'`), use `'\''` to put a single quote in the replacement text.

If the regex or replacement text comes from a shell variable, remember that

- The regex is a BRE, not a literal string.
- In the regex, a newline needs to be expressed as `\n` (which will never match unless you have other `sed` code adding newline characters to the pattern space). But note that it won't work inside bracket expressions with some `sed` implementations.
- In the replacement text, `&`, `\` and newlines need to be quoted.
- The delimiter needs to be quoted (but not inside bracket expressions).
- Use double quotes for interpolation: `sed -e "s/$BRE/$REPL/"`.


-------------

# Remove Columns 
https://stackoverflow.com/questions/2961635/using-awk-to-print-all-columns-from-the-nth-to-the-last
https://unix.stackexchange.com/questions/280955/how-to-process-only-certain-columns-and-ignore-header-and-footer-in-delimited-te


Remove columns 3 and 4 from file and rewrite to outfile

Using awk
```sh
awk '{printf $1FS$2; for (i=5; i <= NF; i++) printf FS$i; print "" }' file # > outfile
```

Using cut
```sh
# -f is fields, 1-2,5- omits 3 and 4
cat file | cut -f1-2,5- # >> outfile2
```

Awk strips leading whitespace
cut does not
```sh
diff outfile*
1,4c1,4
<  
< # 2600x1600 Hz (GTF) hsync: 99.36 kHz; pclk: 353.72 MHz
< Modeline "2600x1600_60.00" 2792 3080 3560 1600 1601 1604 1656 -HSync +Vsync
<  
---
> 
>   # 2600x1600 @ 60.00 Hz (GTF) hsync: 99.36 kHz; pclk: 353.72 MHz
>   Modeline "2600x1600_60.00"  353.72  2600 2792 3080 3560  1600 1601 1604 1656  -HSync +Vsync
> 
```

## deeper dive
cut does not handle multiple/repeating spaces between columns very well
https://stackoverflow.com/questions/18449440/cut-command-does-not-appear-to-be-working
```sh
| tr -s ' ' | cut -d' ' -f1,2

echo "${gtf_output}" | sed -n '3 p' | tr -s ' ' | cut -d' ' -f2-
```

awk
```sh
# print ALL columns
awk '{print $0}' somefile 

# print all but Nth column(s) by
# set column1 to "", then printing all columns
awk '{$1=""; print $0}'  
awk '{$1=$2=""; print $0}' somefile
```

Skip first 6 lines
https://unix.stackexchange.com/a/198066
`FNR` is the row number in each file, while `NR` is the number in the whole input (not an issue when piping).
```c
awk 'FNR > 6 { #process here }' file
```

so to only do line 3...
```sh
echo "${gtf_output}" | awk 'FNR==2 {$1=""; print $0}'
```
# variable arithmetic
```sh
cnt=0
cnt=$((cnt + 1)) # double (( so to prevent subshell in a subshell (

```
# echo and variables

```sh
cnt=0
while [ $cnt -le 4 ]; do
  echo "dq   $cnt"
  echo "dq{} ${cnt}"
  echo 'sq   $cnt'
  echo 'sq{} ${cnt}'
  cnt=$((cnt + 1))
done
```

```
dq   0
dq{} 0
sq   $cnt
sq{} ${cnt}
dq   1
dq{} 1
sq   $cnt
sq{} ${cnt}
dq   2
dq{} 2
sq   $cnt
sq{} ${cnt}
dq   3
dq{} 3
sq   $cnt
sq{} ${cnt}
dq   4
dq{} 4
sq   $cnt
sq{} ${cnt}
```
```
```