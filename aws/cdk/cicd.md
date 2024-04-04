[github actions to call aws with sso](https://aws.amazon.com/blogs/security/use-iam-roles-to-connect-github-actions-to-actions-in-aws/)

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  AWS_REGION: "us-east-1" #Change to reflect your Region

# Permission can be added at job level or workflow level
permissions:
  id-token: write # This is required for requesting the JWT
  contents: read # This is required for actions/checkout
jobs:
  AssumeRoleAndCDKDeploy:
    runs-on: ubuntu-latest
    steps:
      - name: Git clone the repository
        uses: actions/checkout@v3
      - name: configure aws credentials
        uses: aws-actions/configure-aws-credentials@v1.7.0
        with:
          role-to-assume: arn:aws:iam::851725517932:role/AdministratorAccess #change to reflect your IAM role’s ARN
          role-session-name: GitHub_to_AWS_via_FederatedOIDC
          aws-region: ${{ env.AWS_REGION }}
      # Hello from AWS: WhoAmI
      - name: Sts GetCallerIdentity
        run: |
          aws sts get-caller-identity

      # Install AWS CDK CLI
      - name: Install AWS CDK CLI
        run: npm install -g aws-cdk

      # If your project uses npm, install your project dependencies
      - name: Install project dependencies
        run: npm install

      # Deploy with AWS CDK
      - name: CDK Deploy
        run: cdk deploy --all --require-approval never
        env:
          AWS_REGION: ${{ env.AWS_REGION }} # Ensures AWS CDK uses the correct region


```

in the github to aws via federated oidc can you explain the workflow? what is federated oidc and what is the alternative?

ChatGPT

ChatGPT

The GitHub to AWS via Federated OIDC (OpenID Connect) workflow establishes a trust relationship between GitHub Actions and AWS, enabling GitHub Actions workflows to securely assume AWS IAM Roles. This setup allows for secure and efficient management of AWS resources directly from GitHub workflows, leveraging temporary credentials without the need to hardcode or manage long-term AWS access keys in GitHub Secrets.

### Federated OIDC Explained

**OpenID Connect (OIDC)** is an identity layer on top of the OAuth 2.0 protocol. It allows clients to verify the identity of an end-user based on the authentication performed by an Authorization Server, as well as to obtain basic profile information about the end-user in an interoperable and REST-like manner.

**Federated Identity** refers to the linking of a person's electronic identity and attributes, stored across multiple distinct identity management systems. Federated OIDC in the context of GitHub Actions and AWS means using GitHub's identity as a way to authenticate with AWS without needing static access keys.

### Workflow Overview

1. **Setup OIDC Provider in AWS**: You start by creating an OIDC identity provider in AWS IAM that points to GitHub. This involves specifying GitHub as the issuer and adding the GitHub repository as an audience.
    
2. **Create IAM Roles**: Next, you create one or more IAM roles in AWS that trust the OIDC provider. These roles include policies granting permissions needed by your GitHub Actions workflows to operate on AWS resources.
    
3. **Configure GitHub Actions**: In your GitHub repository, you configure a GitHub Actions workflow. This workflow uses the `aws-actions/configure-aws-credentials` action to request a temporary token from AWS STS (Security Token Service) by assuming one of the IAM roles you created. The action authenticates using the OIDC token issued by GitHub to the workflow.
    
4. **Run GitHub Actions Workflows**: When the workflow runs (e.g., on a push event), it authenticates with AWS using the temporary credentials obtained through the OIDC trust relationship. This allows the workflow to deploy resources, update configurations, or perform any tasks allowed by the IAM role’s policies, without using long-term access keys.
    

### Alternative: Static Access Keys

The traditional alternative to federated OIDC is using static AWS Access Keys:

- **Security Risks**: This involves creating an IAM user in AWS, generating access keys, and then adding these keys as secrets in the GitHub repository settings. However, this approach poses security risks if the keys are leaked or mishandled.
    
- **Management Overhead**: There's also the overhead of managing these keys, rotating them periodically for security, and ensuring they are securely stored.
    

### Advantages of Federated OIDC

- **Security**: It enhances security by avoiding the need to store static AWS credentials. The temporary credentials are automatically rotated and have a short lifespan.
    
- **Simplified Credential Management**: There’s no need to rotate keys manually, as temporary credentials are used.
    
- **Auditability**: Actions taken using these credentials can be traced back to specific GitHub Actions runs, improving audit trails.
    
- **Scalability**: Easier to manage at scale, especially for organizations with multiple repositories and AWS resources.
    

Federated OIDC provides a more secure and manageable way to integrate GitHub Actions with AWS, leveraging the strengths of temporary credentials and the trust relationship between the two platforms.

