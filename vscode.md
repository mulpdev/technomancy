# Components

VSCode - The editor
VSCode CLI - Terminal type stuff. `tunnels` command for remote access
VSCode Server - Runs on remote machine to allow tunnels/Remote plugins to work

## APIs and Download links
### Variables
- `$VERSION`
	- `latest` == latest version
	- `commit:<hash>` == specific git commit
		- Accepts hashes for releases https://github.com/microsoft/vscode/releases
	- `MAJ.MIN.PATCH` == version tuple like `1.85.2`
		- [SemVer Format](https://semver.npmjs.com/#syntax-examples) 
- `$PLATFORM`
	- `$PREFIX-$OS-$ARCH`
		- `$PREFIX`
			- No prefix - VSCode packages/installers
			- `server` 
			- `cli`
		- `$OS`
			- `win32`.exe/.msi
				- `win32-archive` .zip (doesn't accept `$PREFIX`,`$ARCH`)
			- `linux` .tar.gz
				- `linux-deb` .deb (doesn't accept `$PREFIX`)
				- `linux-rmp` .rpm (doesn't accept `$PREFIX`)
			- `darwin`
			- `alpine`
		- `$ARCH
			- `x64`
			- `ia32
			- `arm64`
			- `armhf`

- `$QUALITY`
	- `stable`
	- `insider`


### URL
- Tested 3/28/2024
#### Downloading
```
https://update.code.visualstudio.com/$VERSION/$PREFIX-$PLATFORM/$QUALITY
```

#### API
- `version` key is the commit hash
```
# JSON with info about specific version
https://update.code.visualstudio.com/api/versions/$VERSION/$PLATFORM/$QUALITY

# JSON of stable version numbers
https://update.code.visualstudio.com/api/releases/$QUALITY
https://update.code.visualstudio.com/api/releases/$QUALITY/$PLATFORM

#Check for updates DOES NOT WORK but still listed in swagger.json
https://update.code.visualstudio.com/api/update/$PLATFORM/$QUALITY/$VERSION
```

##### Alternate
`https://aka.ms/vscode-server-launcher/$ARCH`
- Not sure if you can pull any specific versions
- `$ARCH`
	-  x86_64-pc-windows-msvc
	- aarch64-pc-windows-msvc
	- x86_64-unknown-linux-gnu
	- aarch64-unknown-linux-gnu
	- unknown-linux-musl
	- apple-darwin-signed
```
New-Item "$HOME\.vscode-server-launcher\bin" -ItemType "directory" -Force
Invoke-WebRequest "https://aka.ms/vscode-server-launcher/x86_64-pc-windows-msvc" -OutFile "$HOME\.vscode-server-launcher\bin\code-server.exe"
[Environment]::SetEnvironmentVariable("Path", [Environment]::GetEnvironmentVariable("Path", "User") + ";$HOME\.vscode-server-launcher\bin", "User")

New-Item "$HOME\.vscode-server-launcher\bin" -ItemType "directory" -Force
Invoke-WebRequest "https://aka.ms/vscode-server-launcher/aarch64-pc-windows-msvc" -OutFile "$HOME\.vscode-server-launcher\bin\code-server.exe"
[Environment]::SetEnvironmentVariable("Path", [Environment]::GetEnvironmentVariable("Path", "User") + ";$HOME\.vscode-server-launcher\bin", "User")
```

## How to identify commit hash  

### API
Json returned from:
`https://update.code.visualstudio.com/api/commits/$QUALITY/$PLATFORM`
### from offline tarball
Extract product file from tarball and extract the hash from this line:
`     "commit": "f80445acd5a3dadef24aa209168452a3d97cc326"`
```
tar -axf vscode-server-linux-x64.tar.gz vscode-server-linux-x64/product.json -O | grep '"commit":' | awk 'match($0, /: "(.+)"/,a){print a[1]}'
```

```
awk 'match($0, /: "(.+)"/,a){print a[1]}'
```

`awk` - command
`'` start and end of awk command
`match(` - match the pattern
`$0,` string being searched (from file or pipe)
` /` start match criteria
`: "(.+)"` the regex to grab everything in second set of quotes
`/,a)` end match criteria, specify variable to store in
`{print a[1]}` print the result (why 1 and not 0 IDK)

# VSCode Server Manual Installation
Open terminal, scp vscode server to target homedir

ssh to target

install on server
```
mkdir -p ~/.vscode-server/bin/<commithash>
cd ~/.vscode-server/bin/<commithash>
mv ~/vscode-server-linux-x64.tar.gz .
tar -xzvf vscode-server-linux-x64.tar.gz --strip-components 1
```
after install use ssh remote plugins
```
open vscode
press f1 and chose remote ssh: connect to host
enter user@domain
a new window may appear, give it password if needed
use file explorer to get to dir list, may require password againi
```

additional
Setting remote.SSH.serverInstallPath so VS code detects it
```
"remote.SSH.serverInstallPath": {
    "work": "/test/location",
    "home": "/foobar"
}
```

```
goto file>preferences>extensions
look for plugins that have:
	yellow triange requesting install for extension in 'SSH:<IP>'
	a blue install in 'SSH:<IP>' button

Install extensions with the blue buttom and reload vscode
```

troubleshooting

```
XHR failed
	goto file > preferences > settings expland the extensions tree
	scroll to remote SSH
	set dropdown for local server download to off
```

if you get an error about tmux, ignore it and restart the terminal by click terminal>new terminal and tmux should reconnect

# Extensions
## Variables

### Marketplace and Version History
![[vscode_publisher_pluginname.png]]

![[vscode_publisher_pluginname2.png]]

![[vscode_publisher_pluginname3.png]]

### `$PUBLISHER`
- Shown with magenta circle
### `$PLUGIN_NAME`
- shown with green circle
### `$VERSION`
- Shown in blue circle
- See "Determining supported VSCode versions" below for identifying relevant versions 

## Download
Last 5 versions may be downloaded directly from the "Version History" tab on the plugin's marketplace page. Otherwise use the following URL

```
https://marketplace.visualstudio.com/_apis/public/gallery/publishers/$PUBLISHER/vsextensions/$PLUGIN_NAME/$VERSION/vspackage
```

## Determining supported VSCode versions
- From git repo of plugin (aka how Marketplace determines what version to give you)
	- Open release or tag version of interest
	- Open `package.json` (probably in root)
	- Search file for `engines` key, and then inside that the `vscode` key
		- ex ` "vscode": "^1.86.0"` in [SemVer Format](https://semver.npmjs.com/#syntax-examples) 
			- `maj.min.patch`
			- `^` - Include everything that does not increment the first non-zero portion of semver
			- `~` - Include everything greater than a particular version in the same minor range
# VSCode editor in browser
https://github.com/coder/code-server

# Sources
https://stackoverflow.com/questions/56671520/how-can-i-install-vscode-server-in-linux-offline
https://gist.github.com/b01/0a16b6645ab7921b0910603dfb85e4fb (Comments)
https://github.com/microsoft/vscode/issues/122396#issuecomment-857915198
https://github.com/microsoft/vscode-python/discussions/20184
https://code.visualstudio.com/docs/remote/vscode-server
https://learn.arm.com/install-guides/vscode-tunnels/
https://stackoverflow.com/questions/62613523/how-to-change-vscode-server-directory