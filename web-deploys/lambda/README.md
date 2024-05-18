
```shell
pnpx cdk bootstrap aws://851725517932/us-east-1
pnpx cdk init app --language=typescript
```


- here goes the handler
	- we will adjust this for a web app later
		- this is an mvp (minimally viable product)
			- later we will use this https://github.com/awslabs/aws-lambda-web-adapter

```typescript

import fs from "fs";
import path from "path";

export const handler = async (event) => {
  const filePath = path.join(process.cwd(), "yourfile.txt");
  try {
    const data = fs.readFileSync(filePath, "utf8");
    console.log(data);
    return {
      statusCode: 200,
      body: data,
    };
  } catch (err) {
    return {
      statusCode: 500,
      body: "Failed to read file: " + err.message,
    };
  }
};

```

- and the file it reads is just a text
```txt
# yourfile.txt
yes hello this is dog
```

- and all this will be in **a subdirectory of** the lib folder of the cdk. here goes the Stack
	- cdk.aws_lambda_nodejs.NodeJsFunction does not work because it uses esbuild which takes a single file as the code and the options object key for the code is not "code" but "entry"
	- the cdk.aws_lambda.Function takes a directory which it will zip, which is what we want

```ts

import * as cdk from "aws-cdk-lib";
import { Construct } from "constructs";
import * as path from "path";
// import * as sqs from 'aws-cdk-lib/aws-sqs';

export class CdkLambdaFsreadfileStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    new cdk.aws_lambda.Function(this, "fsreadfile", {
      runtime: cdk.aws_lambda.Runtime.NODEJS_LATEST,
      handler: "index.handler",
      code: cdk.aws_lambda.Code.fromAsset(
        path.join(__dirname, "services/readfile")
      ),
    });
  }
}

```

