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

# Environment Variables

```powershell
# does work for HOME but not necessarily others
echo $HOME

# show all
env

# show specific
$env:USERPROFILE
```