https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui

# Notes
`.ssh` is found in `C:\Users\<username>`
- also in `$HOME` and `$env:USERPROFILE`

Windows component != service name
- OpenSSH Client == `ssh-agent` service
- OpenSSH Server == `sshd` service

# Install

1. Start > Settings > System > Optional Features
As of 2024-06-12:
- OpenSSH Client is probably already listed under "Added Features"
- OpenSSH Server is probably not and needs to be installed

2. Under "Add an optional feature" section, click "View Features"
3. Search for "OpenSSH"
4. Click checkbox next to OpenSSH Server (and Client if needed)
5. Click Next at bottom
6. Click Add at bottom
7. Start SSH Client and SSH Server
- Use `services.msc` or Powershell
```Powershell
Start-Service ssh-agent
Start-Service sshd
```

8. Set startup type to automatic 
Use `services.msc` or Admin Powershell
```Powershell
Get-Service ssh-agent | Set-Service -StartupType Automatic
Get-Service sshd | Set-Service -StartupType Automatic
```

# Generate SSH keys
Powershell or Gitbash
```
ssh-keygen -t ed25519
```

# Copy SSH public key to remote system
`ssh-copy-id <user>@<remotehost>` (Only available in Windows via GitBash)

Manually copy text from local `$HOME\.ssh\id_ed25519.pub` to `$HOME/.ssh/authorized_keys` on remote

# Optional Setup
`$HOME\.ssh\config` is where you can make aliases
```
Host <some_alias>
	User <remote_username>
	HostName <remote_host>
	#Port <remote_port>
```

Use the alias in place of `user@host` when using `ssh`
- Ex `ssh <some_alias>`