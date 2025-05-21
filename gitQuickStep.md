## Quick setup — if you’ve done this kind of thing before
```bash
git clone git@github.com:asvTmp/step_max10_pet.git
```
**…or create a new repository on the command line**
```bash
echo "# step_max10_pet" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/asvTmp/step_max10_pet.git
git push -u origin main
```
**…or push an existing repository from the command line**
```bash
git remote add origin https://github.com/asvTmp/step_max10_pet.git
git branch -M main
git push -u origin main
```

## Псевдонимы в Git  
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.last 'log -1 HEAD'
```  
