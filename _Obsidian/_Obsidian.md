
`CTRL+e` switches between reading and source
# ToC of Headings
```
	```query
	file:obsidian /^#+ /
	```
```

```query
file:obsidian /^#+ /
```

# Search
See help.obsidian.md/plugins/search

# Plugins manual install
git clone into `.obsidian/plugins`

# Themes manual install
`{VAULT_FOLDER}/.obsidian/themes/{themename}/[theme.css, manifest.json]`
	- {themename} must be exactly as in manifest.json

# CSS snippets manual install
`{VAULT_FOLDER}/.obsidian/snippets/`

```csss
:root {
	--folder-magenta: #f73f9f
}
.theme-dark {
	.side-dock-ribbon {background-color:var(--folder-magenta);}
	.tree-item .is-active {background-color:#c5dc66;}
}
```

# Table
```
| heading1 | heading2 |
| - | - |
| blah1 | blah2 |
```

# Headings
## Links to headers with spaced
- DOES NOT WORK with gitlab 

URL percent encoding
```
[MyLinkName](/Path/To/file.md#Some%20SHeading)
```

/ is relative path from ROOT of obsidian dir

## GitLab and Markdown compatible header links
Headings may not have spaces, that breaks the hyperlinks in GitLab
- not characters `; /`
```
[name](#lowercase_only_after_hash)

[name](./Relative/file.m#lowercase_only_after_hash)
```

## Code highlighting (only in reading mode?)

Prismjs is the underlying code, look at its supported languages for others

```
```arm-asm
```armasm
```nasm

```

# Callouts/Alerts

GitLab and Obsidian don't agree on primary colors of the callout boxes though

```
> [!TYPE] optional colored title text
> Regular text here
> line2

> [!TYPE]- optional colored title text
> Regular text here (collapsible)

> [!TYPE]- optional colored title text
> Regular text here (collapsible)
> ```python
> print("embedded code block")
> ```
```

## Obsidian Callout Colors
Hex codes pulled from DOM elements on help page for callouts. Names in parens are aliases.

<font color="#027aff">Obsidian Title</font> `note info todo`
<font color="#53dfdd">Obsidian Title</font> `abstract (summary tldr)`
<font color="#53dfdd">Obsidian Title</font> `tip (hint important)`
<font color="#44cf6e">Obsidian Title</font> `success (check done)`
<font color="#e9973f">Obsidian Title</font> `question (help faq)`
<font color="#fb464c">Obsidian Title</font> `failure (fail missing)`
<font color="#fb464c">Obsidian Title</font> `danger (error)`
<font color="#fb464c">Obsidian Title</font> `bug`
<font color="#a882ff">Obsidian Title</font> `example`
<font color="#9e9e9e">Obsidian Title</font> `quote (cite)`

## GitLab Callout Colors
GitLab Dark hex codes pulled from `application_dark-<hash>/css` linked to by the element on the page. 
- div element with the class `markdown-alert-<alerttype>`
- Usually a variable `var(--gl-feedback-<alerttype>-text-color)` which is ultimately results
	- `--gl-color-<colorname>--<value>` with a hexcode assignment

<font color="#9dc7f1">GitLab Dark Title</font> `note`
<font color="#91d4a8">GitLab Dark Title</font> `tip`
<font color="#e9be74">GitLab Dark Title</font> `caution`
<font color="#fcb5aa">GitLab Dark Title</font> `warning`
<font color="#cbbbf2">GitLab Dark Title</font> `important`

