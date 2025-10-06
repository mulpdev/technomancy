# Make shortcuts in start menu
In `C:\\ProgramData\Microsoft\Windows\Start Menu\Programs` create a shortcut (ala `.lnk`) to your file. This requires admin privs

Then, open the start menu and search for the exact name of your shortcut. Right Click and choose "Pin to Start"

# Unicode
- Type hexcode numbers, then press `Alt+X`
- Type "U+", the hexcode numbers, then press `Alt+X`
- Character Map
	- `%windir%\system32\charmap.exe`
	- Start > Search "character" and select Character Map (Windows 8+)
	- Start > Accessories > System Tools > Character Map (Windows 7 and earlier)

# Windows Terminal

`Ctrl+C` copies all text as it looks, but with CRLF

Workaround
1. Copy from Windows Terminal `Ctrl+Shift+C`  instead if right click
2. Copy into Notepad++ new file
3. Edit > EOL Conversion > Unix (LF)