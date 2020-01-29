# Impostor's Howto #3: Setting Up Deployment

## Problem

I want to have a robust deployment process that supports environments.
I want to spend as little as possible on hosting.

## Where we pick up prior art

- WSL 1 or 2 with Docker and Docker-Compose installed ([workflow-setup.md](workflow-setup.md)).
- A Google Cloud or AWS virtual machine is set up.
- Dokku is installed on the VM.
- A domain is at hand and its A record points to VM's IP address.

## Solutions

### Set up local tooling

Install `gcloud` as an example of virtual machine provisioning CLI. In commnad line, set up `gcloud`:

``` bash
gcloud config set account <account>@gmail.com
gcloud auth login
gcloud compute ssh <dokku-host> # Remote terminal into dokku host VM
```

### Set up Ghost deployment on Dokku

Assuming the above conditions are met, this is relatively straightforward. I need to have DOMAIN and EMAIL, and APPNAME and APPNAMEDB can be whatever, they're just identifiers.

``` bash
dokku apps:create <APPNAME>
docker pull ghost:latest
docker tag ghost:latest dokku/<APPNAME>:latest
dokku checks:disable <APPNAME>
dokku domains:add <APPNAME> <DOMAIN>
dokku mariadb:create <APPNAMEDB>
dokku mariadb:link <APPNAMEDB> <APPNAME>
dokku config:get <APPNAME> DATABASE_URL
```

The input of the last command should give, amond other stuff, <PWD> to put into the following command:

``` bash
dokku config:set --no-restart <APPNAME> database__connection__user=mariadb database__connection__password=<PWD> database__connection__host=dokku-mariadb-<APPNAMEDB>
database__connection__database=<APPNAMEDB>
dokku --no-restart config:set <APPNAME> url=https://<DOMAIN>
dokku proxy:ports-add <APPNAME> http:80:2368
dokku storage:mount <APPNAME> /opt/<APPNAME>/ghost/content:/var/lib/ghost/content
sudo mkdir -p /opt/<APPNAME>/ghost/content
dokku tags:deploy <APPNAME> latest
dokku letsencrypt <APPNAME>
dokku config:set --no-restart <APPNAME> DOKKU_LETSENCRYPT_EMAIL=<EMAIL>
dokku letsencrypt <APPNAME>
```
