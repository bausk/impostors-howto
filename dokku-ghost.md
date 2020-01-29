``` bash
dokku apps:create <APPNAME>
docker pull ghost:latest
docker tag ghost:latest dokku/<APPNAME>:latest
dokku checks:disable <APPNAME>
dokku domains:add <APPNAME> <DOMAIN>
dokku mariadb:create <APPNAMEDB>
dokku mariadb:link <APPNAMEDB> <APPNAME>
dokku config:get <APPNAME> DATABASE_URL
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
