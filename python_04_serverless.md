# Deploying to AWS via Serverless

From my experience, `serverless` is a much more robust and convenient tool than Zappa. Here we will deploy the app to AWS Lambda.

### Prerequisites

You should have the setup from [Python Tutorial](01_start_and_deployment.md).

Install Serverless and dependencies:

```
npm install -g serverless
npm init -f
npm install --save-dev serverless-wsgi serverless-python-requirements
```

Checkout the AWS Lambda version:

```
git checkout -t v1.3-sls
```

In this version, take a look at how the environment variables are passed through to `serverless.yml`.

### Basic Install

Follow [this tutorial](https://serverless.com/blog/flask-python-rest-api-serverless-lambda-dynamodb/).

Main problem is going to be reducing the size of the deliverable. Example serverless.yaml to do that is like this in my case:

```
# serverless.yml

service: onillambda

plugins:
  - serverless-python-requirements
  - serverless-wsgi

custom:
  wsgi:
    app: app.app
    packRequirements: false
  pythonRequirements:
    slim: true
    dockerizePip: non-linux
    pythonBin: python

package:
  exclude:
  - .venv/**
  - .idea/**
  - .git/**
  - .ebextensions/**
  - .elasticbeanstalk/**
  - .vscode/**
  - scripts/**  

provider:
  name: aws
  runtime: python3.6
  stage: dev
  region: us-east-1

functions:
  app:
    handler: wsgi_handler.handler
    events:
      - http: ANY /
      - http: 'ANY {proxy+}'
```

This example config is mostly derived from the tutorial above and [another example for Plotly](https://github.com/nafeger/serverless-plotly-example/blob/master/serverless.yml).

If you're getting bucket/role problems, manually delete the stack in [CloudWatch](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filter=active), rename the service, and redeploy.

### The Challenge with Redis

The current `v1.3-lambda` deploy script is `scripts/generate_vars.py` only, and requires an additional `credentials/variables.slslambda.json` config file with 2 variables: REDIS_HOST and REDIS_PWD. This is because setting up Elasticache to work properly with the Lambda function is possible but it's just too much work for the scope of this tutorial.

You should obtain values for these variables by provisioning a separate cloud Redis instance, for example at https://redislabs.com.

### Adding a Custom Domain Name

(Disclaimer: along with Redis, this is the part where the limitations and rituals around AWS Lambda are starting to outweigh its benefits; this also teches a good rule of thumb about when NOT to use Lambdas: repackaging a 'normal' web application for Lambda is generally not a very good idea.)

To make the deployed Lambda a fully functional website with an address, we use this [tutorial](https://medium.com/@yhjor/setup-your-aws-api-gateway-with-custom-domain-in-7-steps-86dd32d968a1) to add a custom domain name.

Disclaimer. Adding a custom, non-Route 53 managed domain name to a Lambda function is an unmitigated disaster. Main takeaways:

1. `certbot` is unusable in the case of AWS Lambda, use `acme` by Google.

2. `.cer` format is equivalent to `der` SSL certificate storage format. To convert the `cer` file downloaded from acme link after you complete the guide above, use the following command on each of the certificates in the chain (they are exportable right when viewing them in the Windows Explorer):

```
 openssl x509 -inform der -in cert.cer -out cert.pem
```

### Moving Forward

At this point it became obvious for me that continuing to develop a Flask-based Lambda deployment is not optimal. In the next series, named [JAMstack](../jamstack), I'm going to try converting the Dash app we put together as MVP in this tutorial into a JAMStack-based application split up into services, trying an exciting new set of technologies.
