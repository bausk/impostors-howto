# Web Development Workflow Setup (Windows 10)

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

```
sudo apt update
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget
```


## Debugging any script with Visual Studio Code

```
TBA
```
