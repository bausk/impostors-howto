# Working with Environments Deployed on Dokku

This collects processes and gotchas on deploying Python and React applications and Postgres/TimescaleDB/MariaDB databases to a Dokku server.

## Installing Dokku

1. Check that you have the private certificate that your remote machine has set up for SSH access.

2. Log into the remote machine (AWS uses ubuntu as default username):

```
ssh ubuntu@domain.name
# or, to support a custom private key
ssh -i ~/.ssh/customfile ubuntu@domain.name
```

3. Install Dokku (from [Official docs](http://dokku.viewdocs.io/dokku/getting-started/installation/)):

```
# As of 2021, check link for most current version code
wget https://raw.githubusercontent.com/dokku/dokku/v0.19.13/bootstrap.sh;
sudo DOKKU_TAG=v0.19.13 bash bootstrap.sh
````

4. Install some plugins (check for changes on their official homepages):

Plugins enable Dokku to provide various services to deployed applications, for example databases, HTTPS certificates, and more complex deployment processes like deploying a subfolder from a monorepo.

```
# Letsencrypt
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
# MariaDB plugin: a drop-in replacement for MySQL, for example used by the Ghost blog engine
sudo dokku plugin:install https://github.com/dokku/dokku-mariadb.git mariadb
sudo dokku plugin:install https://gitlab.com/notpushkin/dokku-monorepo
```

## Dokku deployment

### Deploying a Dockerfile-based app

1. Start with a [clean dokku install](http://dokku.viewdocs.io/dokku/getting-started/installation/).

2. Set up new app and docker options for build:

```
dokku apps:create APPNAME
dokku docker-options:add APPNAME build '--build-arg env=production'
```

3. Locally, add the dokku upstream:

```
git remote add APPNAME dokku@YOURDOMAIN.TLD:APPNAME
git push APPNAME master
```

### Adding Docker build time variables

On the Dokku host, start with application creation and fill out build-time variables:

``` bash
# If app wasn't created in previous steps
dokku apps:create <APPNAME>

dokku docker-options:add <APPNAME> build '--build-arg env=production' # Docker build variables
dokku docker-options:add <APPNAME> build '--build-arg POETRY_VERSION=1.0.5' # Docker build variables
```

### Adding runtime variables

``` bash
dokku config:set <APPNAME> APP_SECRET_PRODUCTION=secret # Or whatever variables you need from .env
dokku config:set trader-api-stage POETRY_VERSION=1.0.5
```

### Routing and domains

[To enable HTTPS](https://jonathanmh.com/dokku-with-multiple-domains-and-letsencrypt/):

1. `sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git`

2. `dokku config:set --no-restart someapp DOKKU_LETSENCRYPT_EMAIL=jonathan@example.com`

3. `dokku letsencrypt someapp`

### Adding ports and domains

In Dokku SSH, add ports to connect to your app:

``` bash
dokku domains:add <APPNAME> DOMAINNAME
dokku proxy:ports-add <APPNAME> http:80:5000
```

### Connecting Databases

#### Postgres

Assuming plugin has been installed and application

```
dokku postgres:create <DBSERVICE>
dokku postgres:link <DBSERVICE> <APPNAME>
```

#### TimescaleDB

This also shows how to spin up a plugin-defined container with a custom image instead of the default Postgres image.

1. Create Postgres database from a custom image:

```
export POSTGRES_IMAGE="timescale/timescaledb"
export POSTGRES_IMAGE_VERSION="latest-pg12"
dokku postgres:create testdb
```

2. Fix certificates issue [as described in the article](https://bausk.dev/a-practical-comparison-of-timescaledb-and-influxdb/).

#### MariaDB

Connect MariaDB

#### Accessing database from pgAdmin, CLI or other tools

Find out credentials and exposed port for your DB service:

```
dokku postgres:info <DBSERVICE>
```

If no port is exposed, expose any port from your database:

```
dokku postgres:expose <DBSERVICE>
$ # Will print exposed port
```

Assuming working SSH access to dokku box, set up an SSH tunnel:

```
ssh -L 5433:localhost:<EXPOSED_PORT> ubuntu@domain.name
```

Now you can connect to your database via localhost:5433 and using the database credentials.

### Deploying from Monorepo

```
sudo dokku plugin:install https://gitlab.com/notpushkin/dokku-monorepo
```

**In your repo**, assuming you want to deploy a service from directory `./<DIRNAME>`.

In monorepo root, create a file named `.dokku-monorepo` and input your app name:

```
# project_root/.dokku-monorepo
<APPNAME>=<DIRNAME>
# For example
appname1=path/to/source1
appname2=path/to/source2
```

Now add a new remote dokku@domain.name:<APPNAME>:<OPTIONAL_SUBDOMAIN> and push to it.
