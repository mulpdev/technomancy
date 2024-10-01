# DOCX w/ `docx2txt`
```
find . -type f -iname \*.docx | while read i; do docx2txt < "$i" | grep --color=auto -iH --label="$i" "<FINDME>"; done | less
```

# XLSX w/ `xlsx2csv`
```
find . -type f -iname \*.xlsx | while read i; do xlsx2csv -i < "$i" | grep --color=auto -iH --label="$i" "<FINDME>"; done | less
```

# PPTX w/  unzip/grep
- `grep -P` does Perl regex
- `<a:t>.*?</a:t>` is gist of regex
- `?<` is Perl zero-width look-behind assertion
- `?=` is zero-width look-ahead assertion

```
find . -type f -iname \*.pptx | while read i; do unzip -qc "$i" ppt/slides*.xml | grep -oP '(?<=\<a:t\>).*?(?=\<a:t\>)' | grep --color=auto -iH --label="$i" "<FINDME>"; done | less
```

