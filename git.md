
# Github
```
https://github.com/{USER}/{REPO}/releases/latest

https://github.com/{USER}/{REPO}/releases/latest/tag/{tagname}/latest

- ex: https://github.com/srevinsaju/Firefox-AppImage/releases/tag/firefox
  


git remote add origin git@github.com:{USER}/{REPO}.git
```

```sh
# https://github.com/bergercookie/asm-lsp/releases/download/v0.10.0/asm-lsp-x86_64-unknown-linux-gnu.tar.gz

https://github.com/${org}/${repo}/releases/download/${tagname}/${filename}


org="bergercookie" repo="asm-lsp" tagname="release"; wget "https://github.com/${org}/${repo}/releases/latest/tag/${tagname}/latest"

wget -P /new/dir
wget -O filename

get_release_tag() { 
       org="${1}" 
       repo="${2}"        
	   tagname=$(curl "https://api.github.com/repos/${org}/${repo}/releases/latest" -s | jq .name -r) 
	   echo "${tagname}"                                                         
}
```

if jq binary installed
```sh
# requires jq installed

org=bergercookie repo=asm-lsp ; curl https://api.github.com/repos/$org/$repo/releases/latest -s | jq .name -r
```
## Give READ-ONLY / READ-WRITE ssh key to a repo 
- https://superuser.com/questions/1314064/read-only-access-to-github-repo-via-ssh-key

Use "Deploy keys" project setting:
- project repo page > Settings > Left side Nav > "Deploy Keys"


# Force push new repo branch
```sh
mkdir repo_folder
cd repo_folder  
git init    
git branch -m main  # if necessary

git add *  
git commit -m 'new init'  
git remote add origin git@github.com:mulpdev/my-linux-configs.git  
git push --force --set-upstream origin main
```
# gitignore

Examples:
- github.com/github/gitignore
- https://gitignore.io


global ignore: `~/.config/git/ignore`
repo ignore: `${repo_root}/.gitignore`
no-commit ignore `${repo_root}/.git/info/exclude`