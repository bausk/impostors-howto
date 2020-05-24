# How Do I... in Docker

Tech Stack: Windows 10, Python 3.7+, Poetry package manager, Visual Studio Code for debug and edit.

### Q: How do I debug a Python app with Docker?

A: Set up `Dockerfile` for your Python service, a `docker-compose` config, mount volumes with your code into the container, and run a change watcher on Windows to be able to detect code changes. Then connect the VS Code debugger.

On Windows, use `docker-windows-volume-watcher`.

## Gotchas

`ptvsd` is the preferred debugger for its simplicity.

`ptvsd` is [not compatible with multiprocessing execution](https://github.com/microsoft/ptvsd/issues/1443). Therefore, for web application frameworks that use multiprocessing for change detection (like Sanic does), the solution is not to use `auto_reload` or equivalent. Instead, `os.execv(sys.executable, [sys.executable, __file__] + sys.argv)` is embedded into a service endpoint to invoke process reload. This oneliner is Windows-specific.
