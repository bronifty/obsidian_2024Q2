### Constructs
- a construct is a definition of a resource (eg sqs queue) with properties (eg queue.queueUrl) and a scope (eg this)
```typescript

import * as sqs from '@aws-cdk/aws-sqs';
      
const queue = new sqs.Queue(this, 'MyQueue');
const url = queue.queueUrl; // => A string representing a deploy-time value

```

### Apps and Stacks
- an app is the highest level container of a resource, which contains stacks (environments - eg dev test prod); stacks contain constructs (eg s3 bucket);
- app is the scope (parent object, similar to this in a function closure) of the stack and stack is the scope of a construct

### Synthesize
- means to materialize a cloudformation template (or terraform json) out of the programming code

test