# CDK Course

[CDK Course](https://www.udemy.com/course/aws-typescript-cdk-serverless-react/learn/lecture/37731358#overview)

### Referring to Variables Across Stacks

```typescript
// App
const app = new cdk.App();
const photoStack = new PhotoStack(app, "PhotoStack", {});
new PhotoStackHandler(app, "PhotoStackHandler", {
  targetBucketArn: photoStack.photoBucketArn,
});

// PhotoStackHandler
interface PhotoStackHandlerProps extends cdk.StackProps {
  targetBucketArn: string;
}
export class PhotoStackHandler extends cdk.Stack {
  constructor(scope: Construct, id: string, props: PhotoStackHandlerProps) {
    super(scope, id, props);

    // const targetBucket: string = cdk.Fn.importValue("photoBucket");

    new cdk.aws_lambda.Function(this, "PhotoHandler", {
      runtime: cdk.aws_lambda.Runtime.NODEJS_18_X,
      handler: "index.handler",
      code: cdk.aws_lambda.Code.fromAsset(
        path.join(__dirname, "lambda-handler")
      ),
      environment: {
        TARGET_BUCKET: props.targetBucketArn,
      },
    });
  }
}

// PhotoStack
const photoBucket = new cdk.aws_s3.Bucket(this, "photoBucket", {
  bucketName: `photo-bucket-${this.stackSuffix}`,
  removalPolicy: cdk.RemovalPolicy.DESTROY,
  autoDeleteObjects: true,
});
this.photoBucketArn = photoBucket.bucketArn;
```

### Using Aspects Visitor Pattern to add Tags

- another use case: cdk-nag to enforce best practices (like a linter)

```typescript
// /bin/BucketTagger.ts
import * as cdk from "aws-cdk-lib";
import { IAspect } from "aws-cdk-lib";
import { IConstruct } from "constructs";

export class BucketTagger implements IAspect {
  private key: string;
  private value: string;

  constructor(key: string, value: string) {
    this.key = key;
    this.value = value;
  }

  visit(node: IConstruct): void {
    console.log("Visiting " + node.node.id);

    if (node instanceof cdk.aws_s3.CfnBucket) {
      node.tags.setTag(this.key, this.value);
    }
  }
}

// /bin/app.ts
const tagger = new BucketTagger("label", "PhotoStackBucket");
cdk.Aspects.of(app).add(tagger);
```

### Create CDK Project From Scratch Without CLI

- create folder
- npm init -y
- pnpm add -D @types/node typescript ts-node aws-cdk aws-cdk-lib constructs
- create Launcher.ts file
- create empty stack
- create cdk.json file

### NodeJS Lambda Function

- automatically compiles TS and tree shakes unused deps and bundles with esbuild into a single entrypoint

```typescript
import * as cdk from "aws-cdk-lib";
import { Construct } from "constructs";
import path from "path";

interface LambdaStackProps extends cdk.StackProps {
  spacesTable: cdk.aws_dynamodb.ITable;
}

export class LambdaStack extends cdk.Stack {
  public readonly helloLambdaIntegration: cdk.aws_apigateway.LambdaIntegration;

  constructor(scope: Construct, id: string, props: LambdaStackProps) {
    super(scope, id, props);

    const helloLambdaFunction = new cdk.aws_lambda_nodejs.NodejsFunction(
      this,
      "LambdaFunction",
      {
        runtime: cdk.aws_lambda.Runtime.NODEJS_LATEST,
        handler: "handler",
        entry: path.join(__dirname, "../../services/hello.ts"),
        environment: {
          TABLE_NAME: props.spacesTable.tableName,
        },
      }
    );
    this.helloLambdaIntegration = new cdk.aws_apigateway.LambdaIntegration(
      helloLambdaFunction
    );
  }
}
```

### REST Client (Huachao Mao) for VSCode

- name of file ends in dot http (e.g. test.http)

```http

GET https://wlo3l9xax0.execute-api.us-east-1.amazonaws.com/prod/spaces
###

```
