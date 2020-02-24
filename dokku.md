# Dokku deployment

### Routing and domains

[To enable HTTPS](https://jonathanmh.com/dokku-with-multiple-domains-and-letsencrypt/):

1. `sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git`

2. `dokku config:set --no-restart someapp DOKKU_LETSENCRYPT_EMAIL=jonathan@example.com`

3. `dokku letsencrypt someapp`

### Deploying a Dockerfile-based app

1. Start with a [clean dokku install](http://dokku.viewdocs.io/dokku/getting-started/installation/).

2. Set up new app and docker options for build:

```
dokku apps:create APPNAME
dokku docker-options:add APPNAME build '--build-arg env=production'
```

2. Add monorepo plugin if using monorepo:

```
sudo dokku plugin:install https://gitlab.com/notpushkin/dokku-monorepo
```

3. Locally, add the dokku upstream:

```
git remote add APPNAME dokku@YOURDOMAIN.TLD:APPNAME
git push APPNAME master
```

3. Add https as described in Routing and Domains

### Other Gotchas

- Deploy from monorepo:

```
sudo dokku plugin:install https://gitlab.com/notpushkin/dokku-monorepo

# In .dokku-monorepo
appname1=path/to/source1
appname2=path/to/source2
```
