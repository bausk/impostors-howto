# Deploying a Service with Monorepo

This guide documents deploying a monorepo with build arguments to Dokku.

## Prerequisites

- A Dokku VM with domain name pointing to it.
- A repository created as monorepo

## Solution

Assume we start from scratch with an app.
**In Dokku SSH**, make sure `dokku-monorepo` and `letsencrypt` [are installed](dokku-setup.md)
In Dokku SSH, create an app and recreate your setup of whatever variables are defined in `docker-compose.yml` and `.env`:

``` bash
dokku apps:create <APPNAME>
dokku config:set <APPNAME> APP_SECRET_PRODUCTION=secret # Or whatever variables you need from .env
dokku docker-options:add <APPNAME> build '--build-arg env=production' # Or whatever it is you need during
```

**In your repo**, assume you want to deploy a service from directory `./<DIRNAME>`. In root folder, create a file named `.dokku-monorepo` and input your app name:

```
<APPNAME>=<DIRNAME>
```

Now add a new remote dokku@domain.name:<APPNAME>:<OPTIONAL_SUBDOMAIN> and push to it.

In Dokku SSH, add ports to connect to your app:

``` bash
dokku proxy:ports-add <APPNAME> http:80:5000
```
