# Deploying a Service with Monorepo

This guide documents deploying a monorepo with build arguments to Dokku.

## Prerequisites

### Install Dokku

Log into your machine:

```
ssh ubuntu@domain.name
```

Install Dokku (from [Official docs](http://dokku.viewdocs.io/dokku/getting-started/installation/)):

```
wget https://raw.githubusercontent.com/dokku/dokku/v0.19.13/bootstrap.sh;
sudo DOKKU_TAG=v0.19.13 bash bootstrap.sh
````

### Plugins you might need

Installing plugins (check for changes on their official homepages):

```
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
sudo dokku plugin:install https://github.com/dokku/dokku-mariadb.git mariadb
sudo dokku plugin:install https://gitlab.com/notpushkin/dokku-monorepo
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
```


## Main Track

### Creating new app

On the Dokku host, start with application creation and fill out build-time variables:

``` bash
dokku apps:create <APPNAME>
dokku docker-options:add <APPNAME> build '--build-arg env=production' # Docker build variables
dokku docker-options:add <APPNAME> build '--build-arg POETRY_VERSION=1.0.5' # Docker build variables
```

### Sidetrack: connecting database

``` bash
dokku postgres:create <APPNAME>
dokku postgres:link <APPNAME> <APPNAME>
```

### Setting up runtime variables

``` bash
dokku config:set <APPNAME> APP_SECRET_PRODUCTION=secret # Or whatever variables you need from .env
dokku config:set trader-api-stage POETRY_VERSION=1.0.5
```


### Deploying from monorepo

**In your repo**, assume you want to deploy a service from directory `./<DIRNAME>`. In root folder, create a file named `.dokku-monorepo` and input your app name:

```
<APPNAME>=<DIRNAME>
```

Now add a new remote dokku@domain.name:<APPNAME>:<OPTIONAL_SUBDOMAIN> and push to it.


### Adding ports and domains

In Dokku SSH, add ports to connect to your app:

``` bash
dokku domains:add <APPNAME> DOMAINNAME
dokku proxy:ports-add <APPNAME> http:80:5000
```
