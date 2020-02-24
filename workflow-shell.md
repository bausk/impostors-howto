# Shell Productivity on Windows

Tooling in use: Windows 10, `cmder`, git-for-windows bash.

### Basic commanline operations

```
grep -rl "safetyenv" /etc 2>/dev/null | xargs cat # recursive grep with content show and silenced errors
find . -name "*uwsgi*"
ps -aux
ps -feww
ls -al
```

### Network and ssh cheatsheet

`ssh -R 80:localhost:8000 serveo.net`

### Docker quicklaunches

`docker run --rm --name pg-docker2 -e POSTGRES_PASSWORD=docker2 -d -p 5433:5432 -v //d/docker:/var/lib/postgresql/data postgres`

`docker run --rm -p 10000:8888 -e JUPYTER_ENABLE_LAB=yes -v "$PWD":/home/jovyan/work jupyter/minimal-notebook`


### Integration Gotchas

- `poetry shell` should not be used in bash for Windows, only in cmd/cmder, or else it falls through to wsl shell with no tools and wrong interpreter.

### Git shortcuts

- `git log --all --grep='png'` - Search for matches in Git commit messages.

### Docker shortcuts

- `docker volume ls -q | grep "01" | xargs docker inspect` -- inspect all volumes matching pattern
- `docker volume ls -q | grep "01" | xargs docker volume rm` -- remove all volumes matching pattern

[Other pattern operations over `docker` command](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes)
