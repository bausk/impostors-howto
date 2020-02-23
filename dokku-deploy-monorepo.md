# Deploying a Service with Monorepo

This guide documents deploying a monorepo with build arguments to Dokku.

## Prerequisites

- A Dokku VM with domain name pointing to it.
- A repository created as monorepo

## Solution

Assume we start from scratch with an app.
In Dokku SSH, create an app and recreate your setup of whatever variables are defined in docker-compose.yml.

``` bash
dokku apps:create <APPNAME>
dokku config:set <APPNAME> APP_SECRET_PRODUCTION=secret # Or whatever variables you need from .env
dokku docker-options:add <APPNAME> build '--build-arg env=production' # Or whatever it is you need during
```
