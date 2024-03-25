[hello-world](https://docs.aws.amazon.com/cdk/v2/guide/hello_world.html)

### prerequisites

- aws cli
- aws cdk cli

```shell
npm install -g aws-cdk
which cdk
```

- typescript (tsc and tsserver)

```shell
npm i -g typescript
which tsc
which tsserver
```

### bootstrapping

```shell
# get account id
aws sts get-caller-identity
# get sso identity org region from local config
aws configure get region
```

> cat ~/.aws/config should look something like the following:

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

```shell
cdk bootstrap aws://851725517932/us-east-1
```

[ Delete bootstrap](https://chat.openai.com/c/38e3c864-248a-445a-867a-5c9497ebd54b)

```shell
# run this command in the root of the project with cdk.json
cdk list # get name of cdk project
cdk destroy --app <app_name> # name from prev cmd above
```

### Create App

```shell
cdk init app --language typescript
```

- scaffolds a new app
  - it creates a lib folder where you will develop the library of stacks that will be deployed to your aws account
    - for example, this is where you define constructs (in the constructor of the stack) such as s3 buckets, lambda functions, etc.
  - it creates a bin folder which defines the entry point of the app where env config is set for account specific details

```typescript
// lib/project01-stack.ts
import * as cdk from "aws-cdk-lib";
import { Construct } from "constructs";
// import * as sqs from 'aws-cdk-lib/aws-sqs';

export class Project01Stack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here

    // example resource
    // const queue = new sqs.Queue(this, 'Project01Queue', {
    //   visibilityTimeout: cdk.Duration.seconds(300)
    // });
  }
}
```

```typescript
// bin/project01.ts
#!/usr/bin/env node
import 'source-map-support/register';
import * as cdk from 'aws-cdk-lib';
import { Project01Stack } from '../lib/project01-stack';

const app = new cdk.App();
new Project01Stack(app, 'Project01Stack', {
  /* If you don't specify 'env', this stack will be environment-agnostic.
   * Account/Region-dependent features and context lookups will not work,
   * but a single synthesized template can be deployed anywhere. */

  /* Uncomment the next line to specialize this stack for the AWS Account
   * and Region that are implied by the current CLI configuration. */
  // env: { account: process.env.CDK_DEFAULT_ACCOUNT, region: process.env.CDK_DEFAULT_REGION },

  /* Uncomment the next line if you know exactly what Account and Region you
   * want to deploy the stack to. */
  // env: { account: '123456789012', region: 'us-east-1' },

  /* For more information, see https://docs.aws.amazon.com/cdk/latest/guide/environments.html */
});
```

### Build App

- always build the app whenever you make any changes (it's build/test aka ci step)

  ```shell
  npm run build
  ```

### List Stacks

```shell
cdk ls
```

### Add Construct

```typescript
new s3.Bucket(this, "MyFirstBucketOnUbuntuLaptop", {
  versioned: true,
});
```

### Build App

```shell
npm run build
```

### Synthesize CloudFormation Stack

- output goes to cdk.out folder in root of project

```shell
cdk synth
```

### Deploy Stack

```shell
cdk deploy
```

### Update Stack

```typescript
new s3.Bucket(this, "MyFirstBucketOnUbuntuLaptop", {
  versioned: true,
  removalPolicy: cdk.RemovalPolicy.DESTROY,
  autoDeleteObjects: true,
});
```

### Build App

```shell
npm run build
```

### Synthesize CloudFormation Stack

```shell
cdk synth
```

### View Changeset (Diff)

- cdk cli queries aws account for cloudformation stack and compares it to the synthesized stack

```shell
cdk diff
```
