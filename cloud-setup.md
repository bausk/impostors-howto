# Setting Up Cloud Providers

### Amazon Web Services

We're going to use the following services on AWS:

- Elastic Beanstalk (EBS),
- AWS Lambda,
- AWS Elasticache.

This will guide you through the Amazon CLI setup process. We use two Amazon CLI tools: awsebcli nad awscli.
They should have been installed locally into your project's virtual environment after you ran `pipenv install`.

#### Setting up credentials.

1. Set up a new AWS account.
2. Add a deployment user and group according to the [official manual](https://aws.amazon.com/ru/getting-started/tutorials/set-up-command-line-elastic-beanstalk/). You're going to add the following permissions, one according to the manual, and another one to also allow Lambda deployments:

```
AWSElasticBeanstalkFullAccess
AWSLambdaFullAccess

```

Apart from that, add the following custom permission set:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/*"
        },
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "elasticloadbalancing:*",
            "Resource": "*"
        },

        {
            "Effect": "Allow",
            "Action": [
                "iam:AttachRolePolicy",
                "iam:CreateRole",
                "iam:GetRole",
                "iam:PutRolePolicy",
                "iam:PassRole"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "Stmt1467321765000",
            "Effect": "Allow",
            "Action": [
                "apigateway:*"
            ],
            "Resource": [
                "*"
            ]
        },        
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeAccountAttributes",
                "apigateway:DELETE",
                "apigateway:GET",
                "apigateway:PATCH",
                "apigateway:POST",
                "apigateway:PUT",
                "events:DeleteRule",
                "events:DescribeRule",
                "events:ListRules",
                "events:ListTargetsByRule",
                "events:ListRuleNamesByTarget",
                "events:PutRule",
                "events:PutTargets",
                "events:RemoveTargets",
                "lambda:AddPermission",
                "lambda:CreateFunction",
                "lambda:DeleteFunction",
                "lambda:GetFunction",
                "lambda:GetPolicy",
                "lambda:ListVersionsByFunction",
                "lambda:RemovePermission",
                "lambda:UpdateFunctionCode",
                "lambda:UpdateFunctionConfiguration",
                "cloudformation:CreateStack",
                "cloudformation:DeleteStack",
                "cloudformation:DescribeStackResource",
                "cloudformation:DescribeStacks",
                "cloudformation:ListStackResources",
                "cloudformation:UpdateStack",
                "logs:DescribeLogStreams",
                "logs:FilterLogEvents",
                "route53:ListHostedZones",
                "route53:ChangeResourceRecordSets",
                "route53:GetHostedZone",
                "s3:CreateBucket",
                "s3:ListBucket",
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:PutObject",
                "s3:CreateMultipartUpload",
                "s3:AbortMultipartUpload",
                "s3:ListMultipartUploadParts",
                "s3:ListBucketMultipartUploads"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

After you're done downloading credentials.csv, copy key and secret to `~/.env/credentials` and `~/.env/config`. They are going to look like this:

```
[profile eb-cli]
aws_access_key_id = KEY
aws_secret_access_key = SECRET

[default]
region = us-east-1
```

Now you should be able to deploy stuff to AWS EBS and Lambda using command line tools `eb` and `zappa`. You might want to return to the Python tutorials to read about deployment options and try them out.
