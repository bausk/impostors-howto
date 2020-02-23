This documents Dokku install on a cloud machine.

## Install Dokku

Log into your machine:

```
ssh ubuntu@domain.name
```

Install Dokku (from [Official docs](http://dokku.viewdocs.io/dokku/getting-started/installation/)):

```
wget https://raw.githubusercontent.com/dokku/dokku/v0.19.13/bootstrap.sh;
sudo DOKKU_TAG=v0.19.13 bash bootstrap.sh
````

Install plugins (check for changes on their official homepages):

```
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
sudo dokku plugin:install https://github.com/dokku/dokku-mariadb.git mariadb
sudo dokku plugin:install https://gitlab.com/notpushkin/dokku-monorepo
```

