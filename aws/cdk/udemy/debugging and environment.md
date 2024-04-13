
# 3 Methods to Debug
1. env vars in PROD (dynamically injected by CDK)
2. env vars in vs code debugger launch.json
3. env vars declared in the file

### 1. env vars in prod injected by cdk

```http
@url = https://wlo3l9xax0.execute-api.us-east-1.amazonaws.com/prod/
  
GET {{url}}/spaces
# Authorization: {{token}}
###

POST {{url}}/spaces
content-type: application/json
# Authorization: {{token}}
{
"location": "meow"
}
###
```

- use env vars in prod which are established in the CDK
```typescript
// Launcher.ts
import * as cdk from "aws-cdk-lib";
import { DataStack } from "./stacks/DataStack";
import { LambdaStack } from "./stacks/LambdaStack";
import { ApiStack } from "./stacks/ApiStack";

const app = new cdk.App();
const dataStack = new DataStack(app, "DataStack");
const lambdaStack = new LambdaStack(app, "LambdaStack", {
  spacesTable: dataStack.spacesTable,
});
const apiStack = new ApiStack(app, "ApiStack", {
  spacesLambdaIntegration: lambdaStack.spacesLambdaIntegration,
});


// LambdaStack.ts
import * as cdk from "aws-cdk-lib";
import { Construct } from "constructs";
import path from "path";

interface LambdaStackProps extends cdk.StackProps {
  spacesTable: cdk.aws_dynamodb.ITable;
}

export class LambdaStack extends cdk.Stack {
  public readonly spacesLambdaIntegration: cdk.aws_apigateway.LambdaIntegration;

  constructor(scope: Construct, id: string, props: LambdaStackProps) {
    super(scope, id, props);

    const spacesLambdaFunction = new cdk.aws_lambda_nodejs.NodejsFunction(
      this,
      "LambdaFunction",
      {
        runtime: cdk.aws_lambda.Runtime.NODEJS_LATEST,
        handler: "handler",
        entry: path.join(__dirname, "../../services/spaces/index.ts"),
        environment: {
          TABLE_NAME: props.spacesTable.tableName,
        },
      }
    );

    spacesLambdaFunction.addToRolePolicy(
      new cdk.aws_iam.PolicyStatement({
        effect: cdk.aws_iam.Effect.ALLOW,
        actions: ["s3:ListAllMyBuckets", "s3:ListBucket"],
        resources: ["*"], // bad practice
      })
    );

    spacesLambdaFunction.addToRolePolicy(
      new cdk.aws_iam.PolicyStatement({
        effect: cdk.aws_iam.Effect.ALLOW,
        resources: [props.spacesTable.tableArn],
        actions: [
          "dynamodb:PutItem",
          "dynamodb:Scan",
          "dynamodb:GetItem",
          "dynamodb:UpdateItem",
          "dynamodb:DeleteItem",
        ],
      })
    );

    this.spacesLambdaIntegration = new cdk.aws_apigateway.LambdaIntegration(
      spacesLambdaFunction
    );
  }
}
```

### 2. env vars in launch.json config of vs code debugger
- uses launch.json config with env vars
```json
{
  // Use IntelliSense to learn about possible attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug local file",
      "runtimeArgs": ["-r", "ts-node/register"],
      "args": ["${relativeFile}"],
      "env": {
        "AWS_REGION": "us-east-1",
        "TABLE_NAME": "spaces-table-0e64312a57df"
      }
    }
  ]
}
```
- and to execute
```typescript
import { handler } from "../src/services/spaces";

(async () => {
  try {
    const scanResult = await handler(
      {
        httpMethod: "GET",
      } as any,
      {} as any
    );
    console.log(scanResult);
  } catch (error) {
    console.error(error);
  }
})();
```
### 3. env vars declared in the file
- add the env vars to the file and execute it with ts-node specifying the correct flags
```ts
// test/launcher.ts
import { handler } from "../src/services/spaces/handler";

process.env.AWS_REGION = "us-east-1";
process.env.TABLE_NAME = "spaces-table-0e64312a57df";

handler(
  {
    httpMethod: "GET",
    queryStringParameters: {
      id: "fbe76aea-5aff-434e-85f6-e8f5fc1647ec",
    },
    body: JSON.stringify({
      location: "Best location 2",
    }),
  } as any,
  {} as any
).then((result) => {
  console.log(result);
});

```

```ts
// src/services/spaces/handler.ts
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import {
  APIGatewayProxyEvent,
  APIGatewayProxyResult,
  Context,
} from "aws-lambda";

import { getSpaces } from "./GetSpaces";

const ddbClient = new DynamoDBClient({});

async function handler(
  event: APIGatewayProxyEvent,
  context: Context
): Promise<APIGatewayProxyResult> {
  let response: APIGatewayProxyResult = {
    statusCode: 400,
    body: "Unsupported method",
  };

  try {
    switch (event.httpMethod) {
      case "GET":
        response = await getSpaces(event, ddbClient);

        break;
      default:
        break;
    }
  } catch (error) {
    response = {
      statusCode: 500,
      body: "error",
    };
  }
  return response;
}

export { handler };
```

```ts
// src/services/spaces/GetSpaces.ts
import {
  DynamoDBClient,
  GetItemCommand,
  ScanCommand,
} from "@aws-sdk/client-dynamodb";
import { unmarshall } from "@aws-sdk/util-dynamodb";
import { APIGatewayProxyEvent, APIGatewayProxyResult } from "aws-lambda";

export async function getSpaces(
  event: APIGatewayProxyEvent,
  ddbClient: DynamoDBClient
): Promise<APIGatewayProxyResult> {

  const result = await ddbClient.send(
    new ScanCommand({
      TableName: process.env.TABLE_NAME,
    })
  );
  const unmashalledItems = result.Items?.map((item) => unmarshall(item));
  console.log(unmashalledItems);

  return {
    statusCode: 201,
    body: JSON.stringify(unmashalledItems),
  };
}

```

```shell
ts-node test/launcher.ts
```

