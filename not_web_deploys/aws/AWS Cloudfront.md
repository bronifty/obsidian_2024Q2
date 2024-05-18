### AWS Cloudfront Origin Access Control
explain the significance of this article https://aws.amazon.com/about-aws/whats-new/2024/04/amazon-cloudfront-oac-lambda-function-url-origins/?utm_source=pocket_saves

ChatGPT

ChatGPT

The article discusses a new feature for Amazon CloudFront that enhances security for AWS Lambda function URL origins through Origin Access Control (OAC). This allows only designated CloudFront distributions to access Lambda URLs, thus securing them against unauthorized use and potential web threats. The feature integrates with AWS security tools like AWS Shield and WAF, offering comprehensive protection and ensuring that the content delivery remains fast and reliable worldwide, excluding China. This advancement is significant as it improves security without additional costs and supports easy implementation across multiple AWS tools. For more detailed information, you can view the full article [here](https://aws.amazon.com/about-aws/whats-new/2024/04/amazon-cloudfront-oac-lambda-function-url-origins/?utm_source=pocket_saves).


### AWS Cloudfront as Reverse Proxy
https://www.google.com/search?q=aws+cloudfront+proxy&oq=aws+cloudfront+proxy+&gs_lcrp=EgZjaHJvbWUyCggAEEUYFhgeGDkyCAgBEAAYFhgeMg0IAhAAGIYDGIAEGIoFMg0IAxAAGIYDGIAEGIoFMg0IBBAAGIYDGIAEGIoFMg0IBRAAGIYDGIAEGIoF0gEIODg3NmowajeoAgiwAgE&sourceid=chrome&ie=UTF-8#ip=1



- mapping a custom domain name
```typescript

import * as cdk from "aws-cdk-lib";
import { Construct } from "constructs";

interface ApiStackProps extends cdk.StackProps {
  helloLambdaIntegration: cdk.aws_apigatewayv2.HttpRouteIntegration;
}

export class ApiStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props: ApiStackProps) {
    super(scope, id, props);

    const httpApi = new cdk.aws_apigatewayv2.HttpApi(this, "SpacesApi", {
      apiName: "SpacesApi",
      createDefaultStage: true,
      defaultIntegration: props.helloLambdaIntegration,
    });

    // Setup custom domain
    const domainName = new cdk.aws_apigatewayv2.DomainName(this, 'APIDomain', {
      domainName: 'example.com', // Specify your custom domain
      certificate: cdk.aws_certificatemanager.Certificate.fromCertificateArn(this, 'Cert', 'arn:aws:acm:region:account:certificate/certificate-id'), // Specify your ACM certificate ARN
    });

    // Map the domain to the API
    new cdk.aws_apigatewayv2.ApiMapping(this, 'ApiMapping', {
      api: httpApi,
      domainName: domainName,
      stage: httpApi.defaultStage,
    });

    // Now you can safely output the domain URL
    new cdk.CfnOutput(this, "APIEndpoint", {
      value: domainName.regionalDomainName || "",
    });
  }
}

```