
[cdk v2 guide](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html)
[cdk github wiki](https://github.com/aws/aws-cdk/wiki/)


# CDK v2 Guide

> Prerequisites: 
> 1) aws cli
> 2) aws cdk cli

## Bootstrapping
- needs to be done for every environment
	- creates IAM Roles for the account to deploy resources 
	- if you get an error, don't destroy the resources, just run the command again
		- bootstrapping is idempotent
		- re-bootstrapping is a built-in upgrade to the previous bootstrap

> Steps:
> 1. get your caller identity and region with the aws cli

```shell
# get account id
aws sts get-caller-identity
# get sso identity org region from local config
cat ~/.aws/config
```

> result of cat ~/.aws/config should look something like the following:
```shell
[default]
sso_session = wing
sso_account_id = 851725517932
sso_role_name = AdministratorAccess
region = us-east-1
output = json

[sso-session wing]
sso_start_url = https://d-9067fb1ef3.awsapps.com/start/#
sso_region = us-east-1
sso_registration_scopes = sso:account:access
```

> 2. bootstrap your env with the aws protocol followed by account id and region of your sso identity org

```
cdk bootstrap aws://851725517932/us-east-1
```

