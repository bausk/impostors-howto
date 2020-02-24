# Python Deployment to Serverless Providers

I wanted to try a serverless, modern Python deployment pipeline. As a reference, an example Dash by Plotly project was used.
This is what I've discovered so far.

### Setting Up the Project

Install pipenv. To make it maximally robust, do it in [the following fashion](https://github.com/Miserlou/Zappa/issues/1443) to keep the virtual environment recognizable by tools like `zappa`:
```
export PIPENV_VENV_IN_PROJECT=true
pip install pipenv
pipenv install
export VIRTUAL_ENV=.venv/
```

Now you can install development dependencies, like CLI tools for Amazon Web Services, `eb` and `aws`.

### Setting Up a Cloud Provider

We're going to use several Amazon CLI tools for deployments. Use the [Setting Up Providers](../providers.md) guide.

After you're finished setting up Amazon IAM and setting up the project, you're going to have `eb` and `zappa` CLI commands ready to use.

### AWS Elastic Beanstalk

Let's checkout a basic version of our sample app that uses only Dash/Flask. The following initializes a new Python 3.6 app in your folder, creates a `development` environment and deploys your app with entrypoint defined in `.ebextensions` of the sample repo, and opens the site:

```
git checkout tags/v1.0-basic
eb init -p python-3.6 APPNAME --region us-east-1
eb create development
eb open
```

#### Config files relevant at this stage

In the sample app, folder `.ebextensions/` contains EBS configuration. To execute the basic deployment, the following configuration files are used:

`development.config` -- specifies application entry point and static paths for html/css/images content.

#### Gotchas:

https://docs.aws.amazon.com/en_us/elasticloadbalancing/latest/userguide/elb-api-permissions.html

https://stackoverflow.com/questions/51597410/aws-eks-is-not-authorized-to-perform-iamcreateservicelinkedrole

#### Resources:

[Deploying Dash to AWS EBS](https://www.phillipsj.net/posts/deploying-dash-to-elastic-beanstalk)

[As single container Docker to get Python 3.6](https://docs.aws.amazon.com/en_us/elasticbeanstalk/latest/dg/single-container-docker.html)

### Setting Up Dependencies on EBS

Now that we have the barebone app running, we can learn how to gradually add functionality. Our milestone at this point is being able to get data from a third party source into our app and saving the retrieved data and calculations between user requests. In order to do so, we're going to connect our Dash app to a Google Sheet to extract data, and then connect a Redis in-memory store to be able to both cache requests and save user's data between requests.

#### Setting Up Google Sheets Integration

First let's do the easier integration with Google Sheets that requires only setting up Google credentials and doesn't require provisioning any services of our own.

1. Set up [Google Sheets as described in the integration guide](./02_consuming_data.md).

2. As a result, you should now have googlekey.json file in ./credentials folder.

3. Now checkout the Google Sheets-enabled version of the code:

```
git checkout tags/v.1.1-gspread
```

4. Running the credentials generation script will create `./.ebextensions/gsheets.config` prepared for production. Now you can deploy:

```
pipenv shell
python scripts/generate-ebs.py
eb deploy development
```

Now the application is going to have access to Google Sheets keys and will show data from a Google Sheet . For further reading on integration of Google Sheets, read about the `gspread` [package]().

#### Setting Up Redis for Caching and Storing Session Data

For local development, it will suffice to use a local Docker container. Let's spin up a container on default port and find out its IP:

```
docker run --name redis -p 6379:6379 -d redis:alpine
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' redis
```

Reference document: https://gist.github.com/bahmutov/f09b5895f5bb0f2a13f5

For EBS deployment, checkout the Redis-enabled version:

```
git checkout tags/v1.1-redis
```

We're going to use the Amazon's managed implementation, Elasticache. Three config files were added to support Elasticache/Redis on EBS. Two are from standard AWS docs:

`elasticache.config` -- Specifies the security group for Elasticache, whatever that stuff means,

`options.config` -- Specifies settings for the Redis machine.

Now we need to add config file that will provide an environment variable with the Redis endpoint. Finding out the endpoint cannot be described by code, unfortunately. You need to `eb deploy` before you actually start using Redis because there is no instance set up yet so there is no endpoint to specify. After you deploy, run this command to find out the Redis endpoint:

```
 aws elasticache describe-cache-clusters --show-cache-node-info
```

In the resulting JSON, the URL is filed under CacheClusters.CacheNodes.Endpoint.Address. Add it to the third config file, say `redis.config`, which should read like this:

```
option_settings:
  - option_name: REDIS_HOST
    value: redis-cluster.ameaqx.0001.use1.cache.amazonaws.com:6379
```

Now the cloud deployment will be able to find the Redis server.

#### Setting Up Scripts

We can easily get data about the redis host and other environment into the config files using a couple of scripts.

`scripts/generate_ebs.py` will write two production files into `.ebextensions` folder:

- `redis.config` with Redis connection data, as we did manually above,
- `gsheets.config` with Google authentication.

`scripts/generate_vars.py` will write secrets.config into `.ebextensions` folder with variables specified in `credentials/variables.production.json` if run standalone. It also defines a `write_development` function that we can call on startup to generate any variables emulating EBS config for local development.

`scripts/deploy_ebs.sh` will simply run the two previously defined scripts in sequence and then try to deploy to EBS.

### Next Chapter

[Deploying to AWS Lambda](03_awslambda.md)
