https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_07_02

```sh
#! /bin/sh

global_var=-1
return_via_var() {
	global_var="${1}"
}

return_via_echo() {
	echo "${1}"
}

return_exit_status() {
	cmd;
	return "${?}"
}


myfunc "hello" "arg2"
```


# redirection
`N>&word` and `N<&word` 
- `N` is optional fd, defaults to 0 for input and 1 for output
- `<` `>` `>>` redirection operators
- `&word` 
	- digits of another fd to redirect fd `N` to
	- `-` character to close fd `N`

```sh
# redirect stdout to file out.txt
cmd >out.txt 

# redirect stderr to file err.txt
cmd 2>err.txt 

# redirect stdout to file out.txt, and stderr file err.txt 
cmd >out.txt 2>out.err 

# redirect stdout to file out.txt, and stderr to stdout 
# result: stderr and stdout to file out.txt
#    - kinda sorta like C, "2 points to addr of 1"
cmd >out.txt 2>&1 
```


# command substitution
- avoid backticks, that's a bash-ism anyway
- use `$(...)` which is POSIX, in all modern shells (incl busybox/ash/dash) and CAN BE NESTED w/o escape quoting
- examples
```sh
kill -9 $(ps aux | awk '{if (match($11, "qemu")) print 2$}')

file $(find . -name "*lib*" 2>/dev/null | grep "_v2") # run file on every match

grep -in "subprocess\.run" $(find . -type f -name "*.py")
```

# :- and :+ in variables
```
# :+
pkg_list=${pkg_list:+${pkg_list} }${deb_min}
```
`{pkg_list:+${pkg_list} }` 
- if pkg_list is defined, append itself and add a space (or whatever string is between first `} and second `}

```
# :-
echo ${DOESNOTEXIST:-$DOESEXIST}
```
echo DOESNOTEXIST if it exists, otherwise DOESEXIST (effectively a default value)

# multiline string

```
# <<- ignores the leading \t before the string (for source code indentation purposes)

	multi_str=$(cat <<-EOF
	line1
	line2
	line3
	EOF
	)
	echo "$multi_str" #>> file.txt
```

# Shell script cheat sheet



## ${varname}
`${varname}` expands variable varname
- <font color="magenta">Note:</font> if prepended to a command, then the assignments MUST be separated from command by semicolon

if `foo="abc"` and `foobar="qwerty"` thne `echo ${foo}bar` will print the desired result `abcbar` instead of the variable `$foobar`

You can use optional special chars `? - + =` for other behavior
- `?` Print complaint if not defined
	- `${varname?}` will print "varname: parameter null or not set" UNLESS
	- `${varname?value}` will print value instead
- `-` Default value if UNdefined
	- `${varname-value}` If varname is UNdefined, use value, but do NOT update variable
- `+` Default value if IS defined
	- `${varname+value}` If varname IS defined, use value, but do NOT update variable
- `=` Force use of value and update variable
	- `${varname=value}` Use value and set variable = value 

## pattern match filtering of a variable
https://stackoverflow.com/a/25536935

- `${var#pattern}` - Removes smallest string from the left side that matches the pattern.
- `${var##pattern}` - Removes the largest string from the left side that matches the pattern.
- `${var%pattern}` - Removes the smallest string from the right side that matches the pattern.
- `${var%%pattern}` - Removes the largest string from the right side that matches the pattern.

Here are a few examples:
```bash
foo="foo-bar-foobar"
echo ${foo#*-}   # echoes 'bar-foobar'  (Removes 'foo-' because that matches '*-')
echo ${foo##*-}  # echoes 'foobar' (Removes 'foo-bar-')
echo ${foo%-*}   # echoes 'foo-bar'
echo ${foo%%-*}  # echoes 'foo'
```


https://stackoverflow.com/questions/1561830/shell-matching-a-pattern-using-a-case-statement-which-is-stored-inside-a-variabl

It is possible to match a sub-string in a string without spawning a sub-process (such as `grep`) using the only POSIX compliant methods of [`sh(1p)`](http://man7.org/linux/man-pages/man1/sh.1p.html), as defined in [Section 2.6.2, Parameter Expansion](http://pubs.opengroup.org/onlinepubs/009695399/utilities/xcu_chap02.html#tag_02_06_02).

```sh
# Note:
# Unlike a regular expression, the separator *must* enclose the pattern;
# and it could be a multi chars.

isin() {
    PATTERN=${2:?a pattern is required}
    SEP=${3:-|}
    [ -z "${PATTERN##*${SEP}${1}${SEP}*}" ]
}

# case statement use
PATTERN="|foo bar|baz|bla|"
case "$needle" in
    xyz) echo "matched in a static part" ;;
    *)
        if [ -z "${PATTERN##*|${needle}|*}" ]; then
            echo "$needle matched $PATTERN"
        else
            echo "not found"
        fi
esac

# normal user
for needle in foo bar; do
    isin "$needle" "|hello|world|foo|" && echo "found: $needle"
done

# using ";" as separator
for needle in foo bar; do
    isin "$needle" ";hello;world;foo;" \; && echo "found: $needle"
done

# using the string "RS" as separator
for needle in foo bar; do
    isin "$needle" "RShelloRSworldRSfooRS" RS && echo "found: $needle"
done
```

# Loop

```bash
#!/bin/sh

for (( i=1 ; ((i-100)) ; i=(($i+1)) ))
do
  echo $i
done;
```

```bash
#!/bin/sh

i=1;
while [[ i -le 100 ]] ;
do
  echo $i;
  i=$((i+1));
done;
```


```sh
history | grep "apt install" | while IFS='' read -r line; do echo "${line#*apt install}"; done
```

# Arrays
POSIX sh does not have a named array, you must fake it with `$@`
- NON-POSIX required `S{}` when expanding array elements `$arrayname[42]}`
- `$*` expands to all positional arguments as a single word

```sh
# if array argument count is != 1
if [ '$#' -ne 1 ]; then
   echo "Usage: ${0}"
   exit0
elif [ ]; then
   echo "elif"
else
   echo "else"
fi
# -- important, it distinguishes between options and arguments. esp if begnning with -

set -- "string1" "string2" "string3"

# now use positional args
echo "$1"
echo "$2"

# for loop
for item in "$@"; do
	echo "$item"
done

# appending element
set -- "$@" "appendme"

# remove element
shift x # remove x elements from beginning

# need temp vars and login in a function for remove at index
```

`$(...)` command substitution 
`|` pipe stdout
`|&` pipe BOTH stdout and stderr
`&&` only run if previous command succeeded (exit status == 0)
`||` only run if previous command FAILED (exit status != 0)
`;` run next command no matter what
`&` background job
`\` ignore line break, next line is a continuation

# Rich's posix sh quote/eval trick for saving and restoring $@
Source: https://www.etalabs.net/sh_tricks.html

- Usage
	- `saveme=$(save "$@")` to save off array into variable
	- `eval "set -- $saveme"` to restore `$@` from variable
```
... quoting has prepared $myarray for use with eval command, to restore the positional parameters. Other possibilities such as myarray=$(save *) are also possible as well as programmatic generation of values for the array variable
```

```sh
save() {
	for i do printf %s\\n "$i" | sed "s/'/'\\\\''/g;1s/^/';\$s/\$/' \\\\/" ; done
	echo " " # to stdout is required. Does flush or something. Otherwise chas get appended on restore
}

echo "$@"
myarray=$(save "$@")

set -- foo bar baz boo
echo "$@"

eval "set -- $myarray"
echo "$@"
```


# tests

```sh
[ "$VAR" <test> "2" ]
[ ! <test> "$VAR" ]

[ "$VAR" <test> "$VAR2" ] && echo "$VAR and $VAR2 satisfy test"
[ "$VAR" <test> "$VAR2" ] || echo "$VAR and $VAR2 DO NOT satisfy test" # seems kinda obtuse
```

- AND `-a` `&&` 
- OR `-o` `||` 
- NEGATE `!`

## NUMERICAL EQUALITY
- `-lt -lte -gt -gte -eq -ne` are for NUMERICAL equality

## STRING EQUALITY
- `=` (SINGLE =)POSIX STRING equality
- `==` (DOUBLE =) BASH STRING equality
- `!=` STRING NOT equal
- `>` `<` ASCII order greater than/less than
- `-n` nonzero length
- `-z` zero length

## FILE / PATH
- `-e` exists
- `-f` is regular file
- `-d` is directory
- `-h` is symlink
	- `-L` is symlink AND resolves
- `-b` block device
- `-c` character special file
- `-p` named pipe
- `-s` file size > 0
- `-t` is fd associated with terminal
- `-r` `-w` `-x` is permission set (for id script is running as)
- `-u` `-g` is SUID/SGID bit set
- `-k` is sticky bit set
- `-O` `-G` is owned by user / group (for if running the script)
- `-N` modified since last read
- `-nt` `-ot` left newer than/older than right side
- `-ef` left sign and right side refer to same inode
- `-nt` "newer than", is left value newer than right side by timestamp

## if/else
- remember that `[` is an actual executable and MUST be:
	- surrounded by spaces (or error `[: missing ']'`)
	- terminated by `]`
```sh
# block style
if [ test1 ] && [ test2 ]; then
	...
elif [ test3 ] || [ test4 ]; then
	...
else
	...

# One line
if [ test1 ]; then echo "test1"; elif [ "test2" ]; then echo "test2"; else echo "else"; fi;

```

## case statement
- only one case can match in POSIX
	- bash has "fallthrough" operator `;;&`
```sh
case "$string" in
	*y*) echo "Only y found" ;;
	*Y*) echo "Only Y found" ;;
	*y*|*Y*) echo "COMBO any y|Y found" ;; #one liner, but will not execute in this position because previous matches will happen first
	y|Y) echo "COMBO single y|Y found" ;;
	"$substring") echo "SUBSTRING $substring found" ;; # does NOT work
	*) echo "default Nope"
esac
```

## user input

```sh
prompt="Continue (y/n): "
printf "$prompt"
read confirm # read -p is a BASH-ism, not POSIX??

case "$confirm" in
	y|Y) echo "combo y|Y found" ;;
	n|n) echo "combo n|N found" ;;
	*) echo "default nope, something else was entered"
esac
```
### bash-ism
`read -p "Continue? (y/n): " VAR1`
- `-p ...` is the prompt (no newline)
- `VAR` is the variable to store into (no $)
```sh
PROMPT="Prompt Text: " STOREVAR
```

Crazy POSIX substring parameter expansion
```sh

# test can replace if [ [ ??
test "${string#*"$substring"}" != "$string" && echo "$substring in $string)"

if [ "${string#*"$substring"}" != "$string" ]; then echo "$substring in $string"; else echo "nope"; fi;

```

## getopt in shell script
- calls `optarg` binary to parse arguments

```sh
"myprog [-a | --alpha] [-x | --xray ARGUMENT]"

OPTIONS=$(getopt -a -n myprogname -o ax: --long alpha,xray: -- "$@")
if [ $? -ne 0 ]; then
	echo "bad options supplied"
fi
eval set -- "$OPTIONS" # set $@ back to result of getopt
while true
do
    case "$1" in
        -a|--alpha)
            echo "alpha"
            shift
            ;;
        #...SNIP..
        --)
            shift # skip -- get argument
            break
            ;;
        *)
            echo "default/not implented ${1}"
            exit 1
            ;;
    esac
done
```
- `getopt` call the program
- `-a` enable getopt's alternate mode  so that single and double dash are treated the same
	- ex `-switch` and `--switch` are the same thing
- `-n myprogname` error messages will show up as from `myprogname` rather than `getopt`
- `-o ax:` valid SHORT options are "-a" and "-x ARGUMENT"
- `--long alpha,xray:` valid LONG options are "--alpha" and ""--xray ARGUMENT"
- `-- "$@"` pass in all arguments from this script

# mktemp
`temp_file=$(mktemp)` - makes a file
`temp_file=$(mktemp -d)` - makes a dir

# return value from function
https://stackoverflow.com/a/8743103

sh can't return a string, only an int (typically exit code)

## 1. Echo strings

```bash
lockdir="somedir"
testlock(){
    retval=""
    if mkdir "$lockdir"
    then # Directory did not exist, but it was created successfully
         echo >&2 "successfully acquired lock: $lockdir"
         retval="true"
    else
         echo >&2 "cannot acquire lock, giving up on $lockdir"
         retval="false"
    fi
    echo "$retval"
}

retval=$( testlock )
if [ "$retval" == "true" ]
then
     echo "directory not created"
else
     echo "directory already created"
fi
```

## 2. Return exit status

```bash
lockdir="somedir"
testlock(){
    if mkdir "$lockdir"
    then # Directory did not exist, but was created successfully
         echo >&2 "successfully acquired lock: $lockdir"
         retval=0
    else
         echo >&2 "cannot acquire lock, giving up on $lockdir"
         retval=1
    fi
    return "$retval"
}

testlock
retval=$?
if [ "$retval" == 0 ]
then
     echo "directory not created"
else
     echo "directory already created"
fi
```

## 3. Share variable

```bash
lockdir="somedir"
retval=-1
testlock(){
    if mkdir "$lockdir"
    then # Directory did not exist, but it was created successfully
         echo >&2 "successfully acquired lock: $lockdir"
         retval=0
    else
         echo >&2 "cannot acquire lock, giving up on $lockdir"
         retval=1
    fi
}

testlock
if [ "$retval" == 0 ]
then
     echo "directory not created"
else
     echo "directory already created"
fi
```


# addme

```sh
# run ls -l on each line of output
find /home/user/.local/share/asm*/ -type f -executable | xargs -L1 ls -l

# get last variable to a function
for a in "$@"; do : ; done && printf '%s\n' "$a"

# loop through all
for a in "$@"; do 
	cmd "${a}"
done


```

https://unix.stackexchange.com/a/257116
[](https://unix.stackexchange.com/posts/257116/timeline)

Given the example of the opening post (positional arguments without spaces):

```bash
print_last_arg foo bar baz
```

For the default `IFS=' \t\n'`, how about:

```bash
args="$*" && printf '%s\n' "${args##* }"
```

For a safer expansion of `"$*"`, set IFS (per @StéphaneChazelas):

```bash
( IFS=' ' args="$*" && printf '%s\n' "${args##* }" )
```

But the above will fail if your positional arguments can contain spaces. In that case, use this instead:

```bash
for a in "$@"; do : ; done && printf '%s\n' "$a"
```

Note that these techniques avoid the use of `eval` and do not have side-effects.