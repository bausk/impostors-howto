# Deploying to AWS Lambda

### AWS Lambda

Lambdas are supposed to live around 5 minutes so they don't quite fit into a Python Dashboard-style application
where user actions are piped back to backend via websockets. There are solutions to this but the overall process in cumbersome.

### Deploying with Zappa

Let's continue with the repo from previous chapter. Zappa should be installed, otherwise run `pipenv install`. You should have the command `zappa` available to you.

Let's start a Zappa app. Delete the zappa_settings.json if it exists, and generate a new one with the command:

`zappa init`

Our entry point is currently `application.application`. Add the following settings to `zappa_settings.json`:

```
        "debug": true,
        "slim_handler": true,
        "keep_warm": false,
```

Now we can deploy after generating environment variables into the settings file:

```
pipenv shell
python scripts/generate_vars.py
zappa deploy
```

This command sequence is also available as `deploy_lambda.sh` script.

#### Gotchas:

- Add prefix to be able to serve static files to the corresponding environments.

- Expose `app.server` as variable and point Zappa to it, otherwise the Dash app won't deploy.

#### Resources:

[Serverless Uptime Monitor on Flask](https://hackernoon.com/creating-a-serverless-uptime-monitor-getting-alerted-by-sms-lambda-zappa-python-flask-15c5fb31027)


### AWS Orchestration

Using custom domain (Route53) with AWS Lambda is a bit tricky and [requires configuring CloudFront](https://medium.com/99xtechnology/full-stack-serverless-web-apps-with-aws-189d87da024a). Django has a [walkthrough](https://edgarroman.github.io/zappa-django-guide/walk_static/) dedicated to this topic.

## Other

https://github.com/anaibol/awesome-serverless

[Grand scheme app](http://jmdaignan.com/2018/02/26/metricsdash/)
