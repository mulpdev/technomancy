# Enable Scripts
Local Powershell window
```powershell
Set-ExecutionPolicy RemoteSigned –Scope Process
```

System wide (from Admin prompt)
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
Set-ExecutionPolicy -ExecutionPolicy Unrestriced
```

User
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Set-ExecutionPolicy -ExecutionPolicy Unrestriced -Scope CurrentUser
```

# Environment Variables

```powershell
# does work for HOME but not necessarily others
echo $HOME

# show all
env

# show specific
$env:USERPROFILE
```