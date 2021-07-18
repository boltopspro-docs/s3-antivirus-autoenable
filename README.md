<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/s3-antivirus-autoenable/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# S3 AntiVirus Auto Enable Blueprint

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

![CodeBuild](https://codebuild.us-west-2.amazonaws.com/badges?uuid=eyJlbmNyeXB0ZWREYXRhIjoiSEw5dzZsb1VxNHVXeDJYTjVhTi9qdU1Uamc5SEJ5d29UVmhLaDhCd1FrOElvUU1jSm4yeWlFditNS3ZlTk80dityNVZmNnplQmVCcm1MdlJGaHJKVzNJPSIsIml2UGFyYW1ldGVyU3BlYyI6IndxTlh0Wjg4SXNjLzdLOWMiLCJtYXRlcmlhbFNldFNlcmlhbCI6MX0%3D&branch=master)

This blueprint is a part of the [S3 AntiVirus Setup Instructions](https://github.com/boltopspro-docs/s3-antivirus/blob/master/docs/instructions-overview.md). Please refer to that doc for the setup steps.

This blueprint deploys serverless resources automatically enable S3 AntiVirus protection for new buckets.

* CloudWatch Event Rule and Lambda Functions to handle automatically enable [s3-antivirus-autoenable](https://github.com/boltopspro-docs/s3-antivirus-autoenable) scanning on newly created buckets.
* CloudWatch Event Rules are regional, so you need a Rule for each region.
* Deploy this stack in the regions you want new s3 bucket to be configured for protection automatically.
* You configure the bucket event notifications to a SQS queue in any region. You should use the SQS queue created by the [boltopspro/s3-antivirus](https://github.com/boltopspro-docs/s3-antivirus) blueprint.

Here's a diagram to explain what gets set up with by this blueprint:

![](https://img.boltops.com/boltopspro/blueprints/s3-antivirus/s3-antivirus-auto-enable-2.png)

## Usage

1. Add blueprint to your Gemfile
2. Configure configs/s3-antivirus-autoenable values
3. Deploy blueprint

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "s3-antivirus-autoenable", git: "git@github.com:boltopspro/s3-antivirus-autoenable.git", submodules: true
```

## Configure

Configure the `configs/s3-antivirus-autoenable/params` files.

    LONO_ENV=development lono seed s3-antivirus-autoenable

The generated files in `configs/s3-antivirus-autoenable` folder look something like this:

    configs/s3-antivirus-autoenable/
    └── params
        └── development.txt

Here some example variables:

configs/s3-antivirus-autoenable/variables/development.txt:

```ruby
# SQS Queue can be in any region.
# Grab it from the s3-antivirus CloudFormation Console Outputs Tab
SqsArn=arn:aws:sqs:us-west-2:112233445566:s3-antivirus-Queue-2L0Q$EXAMPLE # required
```

## Deploy

Let's deploy now with [lono cfn deploy](https://lono.cloud/reference/lono-cfn-deploy/).

    lono cfn deploy s3-antivirus-autoenable --sure

### Deploy to Multiple Regions

To deploy to additional regions use `AWS_REGION`.

    AWS_REGION=us-west-2 lono cfn deploy s3-antivirus-autoenable --sure
    AWS_REGION=us-east-1 lono cfn deploy s3-antivirus-autoenable --sure

To deploy to all regions:

    $ aws ec2 describe-regions --query "Regions[].{Name:RegionName}" --output text | sort > /tmp/regions.txt
    $ for i in $(cat /tmp/regions.txt) ; do AWS_REGION=$i lono cfn deploy s3-antivirus-autoenable --sure ; done

You can also adjust the `/tmp/regions.txt` to target only the regions you wish.
