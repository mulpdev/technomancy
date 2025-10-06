
```sh
# recursive, insensitive, number
grep -rin

#  list file only 
grep -lrin

# invert match
grep -v 

# color highlight
grep --color

# multiple patterns (lowercase e)
grep -e "pattern1" -e "pattern2"
```

# Regex


## Metacharacters
- **Literals:** Matches the exact character.
- `.` : (dot): Matches any single character (except newline).
- `*`: Matches zero or more occurrences of the preceding character or group.
- `+`: Matches one or more occurrences of the preceding character or group (ERE and PCRE, or escaped in BRE: `\+`).
- `?`: Matches zero or one occurrence of the preceding character or group (ERE and PCRE, or escaped in BRE: `\?`).
- `^`: Matches the beginning of a line.
- `$`: Matches the end of a line.
- `[ ]`: Matches any one character within the brackets.
    - `[a-z]`: Matches any lowercase letter.
    - `[^abc]`: Matches any character not `a`, `b`, or `c`.
- `{n}`: Matches exactly `n` occurrences of the preceding element (ERE and PCRE, or escaped in BRE: `\{n\}`).
- `{n,}`: Matches `n` or more occurrences.
- `{n,m}`: Matches between `n` and `m` occurrences.
- `|`: Acts as an OR operator, matching either the expression before or after it (ERE and PCRE, or escaped in BRE: `\|`).
- `()`: Groups expressions together (ERE and PCRE, or escaped in BRE: `\(\)`).
- `\`: Escapes the special meaning of the next character, treating it as a literal.

## Basic Regex
-  metacharacters subset `?`, `+`, `{`, `|`, `(`, and `)` are <font color="cyan">LITERAL</font> and need to be escaped with a backslash (`\`) to be interpreted as <font color="magenta">SPECIAL</font> characters rather than literal ones
```sh
grep "basic-regex"
```

## Extended Regex
 - metacharacters are <font color="magenta">SPECIAL</font> and need to be escaped with a backslash (`\`) to be interpreted as <font color="cyan">LITERAL</font> characters rather than literal ones
```sh
grep -E "regex"
```

## Perl-Compatible Regular Expressions (PCRE)
- PCRE offers a wider range of advanced features and syntax elements, similar to those found in Perl.
```sh
grep -P "regex"
```