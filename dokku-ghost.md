dokku apps:create bausk-dev-prod
docker pull ghost:latest
docker tag ghost:latest dokku/bausk-dev-prod:latest
dokku checks:disable bausk-dev-prod
dokku domains:add bausk-dev-prod bausk.dev
dokku mariadb:create bausk-dev-prod-db
dokku mariadb:link bausk-dev-prod-db bausk-dev-prod
dokku config:get bausk-dev-prod DATABASE_URL
dokku config:set --no-restart bausk-dev-prod database__connection__user=mariadb database__connection__password= database__connection__host=dokku-mariadb-bausk-dev-prod-db database__connection__database=bausk_dev_prod_db
dokku --no-restart config:set bausk-dev-prod url=https://bausk.dev
dokku proxy:ports-add bausk-dev-prod http:80:2368
dokku storage:mount bausk-dev-prod /opt/bausk-dev-prod/ghost/content:/var/lib/ghost/content
sudo mkdir -p /opt/bausk-dev-prod/ghost/content
dokku tags:deploy bausk-dev-prod latest
dokku letsencrypt bausk-dev-prod
dokku config:set --no-restart bausk-dev-prod DOKKU_LETSENCRYPT_EMAIL=bauskas@gmail.com
dokku letsencrypt bausk-dev-prod
