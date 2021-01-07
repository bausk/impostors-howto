# Web Development Workflow Setup (Windows 10)

# Windows Gotchas

- Project files should be located on a native **WSL2 filesystem**. If no WSL2 is installed, keep them on Windows. The reason for this is that WSL 2 will follow through with file changes for (most) build watchers and WSL1 won't properly mount files on Windows filesystem.
- All docker-compose operations should preferably be started from a **WSL2 shell**. If no WSL2 is installed, then from **Windows shell** on runtime and WSL on build.


## Set up WSL

Generate SSH key:
```
TBA
```

Run `nano ~/.bashrc` and add at the end of file:

```
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_rsa
# or, if you want to import from Windows host
ssh-add -- < /mnt/c/Users/bausk/.ssh/id_rsa
```

`Ctrl+X` To exit, `y` to save changes

## Set Up Windows Terminal

Install your preferred fonts. My current choice is [Iosevka SS05 Term](https://github.com/be5invis/Iosevka/releases), a very narrow but very readable font with Fira Mono punctuation style and ligatures.

In Windows Terminal Settings, replace the Powershell block with Gitbash shell:
```
{
  // Make changes here to the powershell.exe profile.
  "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
  "name": "Gitbash",
  "commandline": "%PROGRAMFILES%/git/usr/bin/bash.exe -i -l",
  "hidden": false
},
```

In the same settings file, add defaults and list clauses to support your font of choice, in my particular case:

```
"defaults":
{
    // Put settings here that you want to apply to all profiles.
    "fontFace": "Iosevka Term SS05",
    "fontSize": 12
},
"list":
...
```

## Set Up Python Development

1. Install `pyenv` on WSL.

``` bash
# Install pyenv with instructions from https://github.com/pyenv/pyenv-installer, currently:
sudo apt update && sudo apt install -y build-essential git libexpat1-dev libssl-dev zlib1g-dev libncurses5-dev libbz2-dev liblzma-dev libsqlite3-dev libffi-dev tcl-dev linux-headers-generic libgdbm-dev libreadline-dev tk tk-dev
curl https://pyenv.run | bash
# Complete installation by following CLI instructions
pyenv install 3.9.1
pyenv global 3.9.1

```

## Set Up Node.js Development

1. Install nvm, node, and yarn on WSL

``` bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
nvm install node
nvm use node
npm install -g yarn
```

## Debug with Visual Studio Code

```
TBA
```
