# Mirza AWS CloudFormation

## Initial steps (in AWS console)
- Create AWS account
- Using default VPC for now
- Create IAM users
- Create key in AWS
- Import domains into Route53
- Create SSL certificates for domains in ACM

## Edit parameters in mirza-web-env.parameters.json

Set parameters in `mirza-web-env.parameters.json`

### Required parameters
  - KeyName ✅
  - EnvironmentName: (development, qa, production) ✅
  - DomainName: domain name for the app environment (dev.heymirza.dev, qa.heymirza.dev, prod.heymirza.dev) ✅
  - DBPassword: the database password for RDS
  - ACM SSL certificate ARN: (create the certificate in ACM and copy/paste the ARN from details) ✅

### Optional parameters
  - Root domain: (optional, defaults to: heymirza.dev) ✅
  - EBHostedZoneId: HostedZoneId is required to specify an ALIAS target to the Elastic Load Balancer. HostedZoneId is specific to each region and can be found here: https://docs.aws.amazon.com/general/latest/gr/elb.html . Default is set to the HostedZoneId for us-east-2.
  - NetworkStackName: Used to get the security group IDs to associate with resources. Defaults to mirza-network.
  - MirzaInitStackName: Used to get the Elastic Beanstalk app name from the app created in `./init-mirza`

## Run CloudFormation
- Run: `./create-network` to create all required network security groups
- Run: `./init-mirza` to create Elastic Beanstalk app. This is a one-time creation, as environment stacks live underneath the main app
- Run: `./create-mirza-web-env.sh STACK_NAME_HERE` or `./update-cloudformation.sh STACK_NAME_HERE`
  - Stack name follows the pattern `{app_name}-{env_name}`

## Network
Security groups are created in ./create-network which uses the template `network.template.yml`.

Security group IDs are exported from this stack. The network stack name (default: mirza-network) is used in `mirza-web-env.template.yml` to find the security group IDs to associate with resources.

**Gotcha:** EC2 resources created in the default VPC use the `Security Group Name` whereas RDS resources use the `Security Group ID`. This is only because we're using the Default VPC.

## Route53 in CloudFormation
HostedZoneId is required to specify an ALIAS target to the Elastic Load Balancer. HostedZoneId is specific to each region and can be found here: https://docs.aws.amazon.com/general/latest/gr/elb.html

Since we're only deploying to us-east-2 currently, this is set as a default parameter. TODO: Make this more dynamic (if we were to deploy to other regions later).

## Create Postgres DB in CloudFormation
- Create RDS postgres ✅
- Security groups ✅

## SSL
SSL certicate is managed in ACM. Before running CloudFormation scripts, create this in the AWS console and put the certificate ARN into `mirza-web-env.parameters.json`.

## To SSH into EC2 instance
`ssh -i ~/.ssh/mirza-dev.pem ec2-user@ec2-3-141-30-133.us-east-2.compute.amazonaws.com`
Replace ec2 hostname with hostname from AWS console

## Deployment of app (on each git push)
- CI
- Elastic Beanstalk

CircleCI

