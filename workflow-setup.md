# Web Development Workflow Setup (Windows 10)

# Windows Gotchas

- Project files should be located on a native **WSL2 filesystem**. If no WSL2 is installed, keep them on Windows. The reason for this is that WSL 2 will follow through with file changes for (most) build watchers and WSL1 won't properly mount files on Windows filesystem.
- All docker-compose operations should preferably be started from a **WSL2 shell**. If no WSL2 is installed, then from **Windows shell** on runtime and WSL on build.


## WSL 2

You can work on WSL v.1 or switch to WSL 2.

## Setting up WSL

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

## Setting up Python development

I install pyenv on both host Windows 10 machine and the WSL.

``` bash
# On WSL / WSL2:
sudo apt update
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget
pip3 install pyenv # pip should be installed on WSL
```


## Debugging any script with Visual Studio Code

```
TBA
```
