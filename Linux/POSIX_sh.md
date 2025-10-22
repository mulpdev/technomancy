https://unix.stackexchange.com/questions/444946/how-can-we-run-a-command-stored-in-a-variable

Table of Contents
- [shell operators](#shell%20operators)
    - [chaining](#chaining)
    - [pipes](#pipes)
    - [redirection](#redirection)
    - [multiline string](#multiline%20string)
- [if-elif-else and tests](#if-elif-else%20and%20tests)
    - [test](#test)
        - [LOGICAL EQUALITY](#LOGICAL%20EQUALITY)
        - [NUMERICAL EQUALITY](#NUMERICAL%20EQUALITY)
        - [STRING EQUALITY](#STRING%20EQUALITY)
        - [PATH EQUALITY](#PATH%20EQUALITY)
- [Loops](#Loops)
- [Functions](#Functions)
- [Arrays](#Arrays)
    - [Script and Function array](#Script%20and%20Function%20array)
    - [Setting array arguments](#Setting%20array%20arguments)
    - [Get last variable in array](#Get%20last%20variable%20in%20array)
- [Rich's posix sh quote/eval trick for saving and restoring $@](#Rich's%20posix%20sh%20quote/eval%20trick%20for%20saving%20and%20restoring%20$@)
- [case statement](#case%20statement)
- [user input](#user%20input)
    - [POSIX sh](#POSIX%20sh)
    - [bash](#bash)
    - [using POSIX substring parameter expansion](#using%20POSIX%20substring%20parameter%20expansion)
- [getopt in shell script](#getopt%20in%20shell%20script)
- [variables](#variables)
    - [${varname}](#${varname})
    - [:- and :+ in variables](#:-%20and%20:+%20in%20variables)
    - [pattern match filtering of a variable](#pattern%20match%20filtering%20of%20a%20variable)
- [command substitution](#command%20substitution)
- [Path Snippets](#Path%20Snippets)
	- [escape all backslashes](#escape%20all%20backslashes)
	- [is path absolute or relative](#is%20path%20absolute%20or%20relative)
	- [directory, filename, filestem, extension](#directory,%20filename,%20filestem,%20extension)
- [Run command in variable](#Run%20command%20in%20variable)
	
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

# shell operators
`;` command separator, run next command unconditionally
`\` ignore line break, next line is a continuation
`cmd &` background job
## chaining
exit code  0 is SUCCESS, anything else is not
- `&&` only run if previous command succeeded (exit status == 0)
- `||` only run if previous command FAILED (exit status != 0)
- run `cmd1`, if it succeeds run `cmd2` else run `cmd3` 
```sh
cmd1 && cmd2 || cmd3
```
## pipes
- `|` pipe STDOUT to STDIN of next command
- `|&` pipe STDOUT/STDERR to STDIN of next command
## redirection
https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_07_02

`N>&word` and `N<&word` 
- `N` is optional fd, defaults to 0 for input redirection and 1 for output redirection
- `<` `>` `>>` redirection operators
- `&word` 
	- digits of another fd to redirect fd `N` to
	- `-` character to close fd `N`

> [!tip]
> PIPES have higher operator precedence and are created before any I/O redirection occurs. For example in this line
> ```bash
> command 2>&1 >/dev/null | grep 'something'
> ``` 
> 1. fd 0, 1, and 2 are (aka IN, OUT, and ERR) are process specific file descriptors that default to STDIN, STDOUT, and STDERR for shell programs
> 2. PIPE is set up so that fd1 of `command` refers/points to fd0 of `grep`
> 3. `2>&1` is first redirect interpreted and sets fd2 of `command` to the same fd referred/pointed to fd1 `command` (STDIN of `grep`)
> 4. `>/dev/null` is second redirect interpreted, and sets fd1 of `command` to refer/point to `/dev/null` (WITHOUT effecting any previous redirections)

## multiline string
the `<<-` forces ignoring \t before the string (like for source code indentation)
```sh
multi_str=$(cat <<-EOF
	line1
	line2
	line3
	EOF
)
echo "$multi_str" #>> file.txt
```
# if-elif-else and tests
`[` is an executable, and functional identical to  `test` except that
- it requires a closing `]` 
- brackets are surrounded by spaces (or error `[: missing ']'`)

```sh
test CONDITION

if [ "${VAR1}" -gt 9000 ] && [ "${VAR2}" == "pattern" ]; then
	...
elif [ ! -e "$MYPATH" ]; then
	...
fi
```
## test 
see `man 1 test`
Can also check symlinks, block devices, character special, pipe, terminal related, file size > 0, effective owner/group
### LOGICAL EQUALITY
- `-a` `&&` AND
- `-o` `||` OR
- `!` NEGATE
### NUMERICAL EQUALITY  
`-lt -lte -gt -gte -eq -ne` 
### STRING EQUALITY
- `=` (<font color="green">SINGLE</font> =) <font color="green">POSIX</font> STRING equality
- `==` (<font color="red">DOUBLE</font> \==) <font color="crimson">BASH</font> STRING equality
- `!=` STRING NOT equal
- `-n` nonzero length
- `-z` zero length
- `>` `<` ASCII order greater than/less than
### PATH EQUALITY
- `-e` exists
- `-f` `-d` is regular file, is directory
- `-r` `-w` `-x` user permission bit set (for id script is running as)
- `-u` `-g` `-k` is SUID/SGID/sticky bit set
- `-nt` `-ot` left newer than/older than right side
# Loops
For loop
```bash
for (( i=1 ; ((i-100)) ; i=(($i+1)) ))
do
  echo $i
done;
```
While loop
```bash
i=1;
while [[ i -le 100 ]] ;
do
  echo $i;
  i=$((i+1));
done;
```
While lines of output
```sh
history | grep "apt install" | while IFS='' read -r line; do echo "${line#*apt install}"; done
```

Files in directory
```sh
# DO NOT put quotes around path/* or it breaks
for file in /path/to/directory/*; do 
  echo "${file}"
done
```
# Functions
Functions are run in a sub shell, so use the array variables for arguments

sh can't return a string, only an int (the exit code)
- `$?` will be an ==integer== exit code (can be set with `echo "3"`
- ==strings== must be returned via:
	- global variable 
	- command substitution, which captures output generated by all commands executed in the function
```sh
global_var=""
myfunc() {
  ls -l
  echo "foo"
  global_var="bar"
}

myfunc "Hello" "World"
# $? will be exit code of last command or final echo "x"
# $global_var will be "bar"
# output=$(myfunc "Hello" "World") will be the output all commands
```

https://stackoverflow.com/a/8743103
# Arrays
POSIX sh does not have a named array, you must fake it with `$@`
- NON-POSIX required `${}` when expanding array elements `$arrayname[42]}`
- `$*` expands to all positional arguments as a single word
## Script and Function array
```sh
func() {
        # expects 2 arguments
        echo "FUNC numargs ${#}"
        fplus1=$(($#+1))
        j=1
        while [ "${j}" -le "${fplus1}" ] ; do
                arg=$(eval echo \$"${j}")
                echo "FUNC arg${j} '${arg}'"
                j=$((j + 1))
        done
}

############## MAIN
echo "SCRIPT numargs ${#}"
splus1=$(($#+1))
i=1
while [ "${i}" -le "${splus1}" ] ; do
        arg=$(eval echo \$"${i}")
        echo "SCRIPT arg${i} '${arg}'"
        i=$((i + 1))
done

func "A" "B"
```

## Setting array arguments
```sh
echo "SCRIPT numargs ${#}"
splus1=$(($#+1))
i=1
while [ "${i}" -le "${splus1}" ] ; do
        arg=$(eval echo \$"${i}")
        echo "SCRIPT arg${i} '${arg}'"
        i=$((i + 1))
done

# modify $@ with set
#   the -- part of the command is important! It distinguishes 
#   between options and arguments. esp if begnning with -
set -- "string1" "string2" "string3"

echo "SET numargs ${#}"
splus1=$(($#+1))
i=1
while [ "${i}" -le "${splus1}" ] ; do
        arg=$(eval echo \$"${i}")
        echo "SET arg${i} '${arg}'"
        i=$((i + 1))
done
```

## Get last variable in array
without using `eval` and with no side effects
-  https://unix.stackexchange.com/a/257116
```sh
for a in "$@"; do : ; done && printf '%s\n' "$a"
```

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
# case statement
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

# user input 
## POSIX sh
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
## bash
`read -p "Continue? (y/n): " VAR1`
- `-p ...` is the prompt (no newline)
- `VAR` is the variable to store into (no $)
```sh
PROMPT="Prompt Text: " STOREVAR
```
## using POSIX substring parameter expansion
```sh
# test can replace if [ [ ??
test "${string#*"$substring"}" != "$string" && echo "$substring in $string)"

if [ "${string#*"$substring"}" != "$string" ]; then echo "$substring in $string"; else echo "nope"; fi;
```

# getopt in shell script
- calls `optarg` binary to parse arguments

```sh
"myprog [-a | --alpha] [-x | --xray ARGUMENT]"

OPTIONS=$(getopt -a -n "${0}" -o ax: --long alpha,xray: -- "$@")
if [ "${?}" -ne 0 ]; then
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
	- `:` means option takes a REQUIRED argument
	- <font color="cyan">OPTIONAL</font> args
		- `::` is ==GNU extension== for OPTIONAL argument
		- if using `:` then there is no difference between a short option with and without an argument. The arg count from optarg will imply an argument but it's set to the special value `--` which is NOT the string "--" (why?) 
- `--long alpha,xray:` valid LONG options are "--alpha" and ""--xray ARGUMENT"
- `-- "$@"` pass in all arguments from this script

https://unix.stackexchange.com/a/659516
> A simple short option is a '-' followed by a short option character[...] If the option has an optional argument, **_it must be written directly after the option character_** if present.

> A long option normally begins with '--' [...] If the option has an optional argument, **_it must be written directly after the long option name, separated by '='_**, if present (if you add the '=' but nothing behind it, it is interpreted as if no argument was present; this is a slight bug, see the BUGS).
# variables
variables are assigned with NO SPACES around = `var="value"`
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
## :- and :+ in variables
```sh
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


# command substitution
- avoid backticks, that's a bash-ism anyway
- use `$(...)` which is POSIX, in all modern shells (incl busybox/ash/dash) and CAN BE NESTED w/o escape quoting
- captures ALL output
- examples
```sh
kill -9 $(ps aux | awk '{if (match($11, "qemu")) print 2$}')

file $(find . -name "*lib*" 2>/dev/null | grep "_v2") # run file on every match

grep -in "subprocess\.run" $(find . -type f -name "*.py")
```

# Path Snippets
## escape all backslashes
```sh
if [ -n "${WINDIR}" ]; then
  # escape all \ for Windows Path
  tgz_file_path=$(echo "${tgz_file_path}" | sed 's/\\/\\\\/g') 
fi
```

## is path absolute or relative
```sh
# do a leftmost filter of variable against with single char string '/'
# if left most character is a / it will be removed
# otherwise the result string will be identical

if [ "${1}" != "${1#/}" ]; then
  folder_path="${1}" # absolute path
else
  folder_path="./${1}" # relative
fi
```

## directory, filename, filestem, extension
```sh
var="path/to/somefile.ext"

directory="${var%/*}" # path/to/
filename="${var##*/}" # somefile.ext
filestem=$(tmp="${var##*/}" && echo "${tmp%.*}")   # somefile
extension=$(tmp="${var##*/}" && echo "${tmp##*.}") # ext
```

# Run command in variable
```sh
# Less problematic to use array
set -- "prog" "arg1" "option"
"${@}"

# This works, but may have string issues
"${var}"
```