node package manager

Default install is local to project
```
npm install --global packagename
```

npm error code SELF_SIGNED_CERT_IN_CHAIN

Windows
download .crt (not .pem)
```
`npm config set cafile "C:\temp\trustedcert.cer"`
```