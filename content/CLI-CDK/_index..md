---
title: "4. Install tools and configure AWS CLI, CDK"
weight: 4
draft: false
---

## Objective
Install the AWS CLI, configure credentials, install Node.js/npm (if needed), and install the AWS CDK to prepare for deploying the pipeline with CDK.

## 4.1 Install AWS CLI (AWS CLI v2) & verify

### macOS (Homebrew)
```bash
brew install awscli
```

### Linux (x86_64)
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### Linux (ARM aarch64)
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2_aarch64.zip"
unzip awscliv2_aarch64.zip
sudo ./aws/install
```

### Windows
Download the MSI installer from: https://aws.amazon.com/cli/  
Or use Chocolatey:
```powershell
choco install awscli
```
![Illustration](/images/download.png)
### Verify
```bash
aws --version
# e.g.: aws-cli/2.x.x Python/3.x ...
```
![Illustration](/images/version.png)

## 4.2 Configure AWS credentials (aws configure)

> Security tip: Create a separate IAM user for the workshop with programmatic access. For a quick demo you can use `AdministratorAccess`, but in production prefer least-privilege.

1. Create an IAM User on the AWS Console [https://aws.amazon.com/console/](https://aws.amazon.com/console/) → **IAM** → **Users** → **Create user**  
   - Select **Programmatic access**  
![Illustration](/images/IAM1.png)
   - Attach appropriate policy  
![Illustration](/images/IAM2.png)
![Illustration](/images/IAM3.png)
   - Save the **Access Key ID** and **Secret Access Key**
![Illustration](/images/IAM4.png)
2. On your local machine (configure credentials):
```bash
aws configure
# Enter in order:
# AWS Access Key ID [None]: <ACCESS_KEY_ID>
# AWS Secret Access Key [None]: <SECRET_ACCESS_KEY>
# Default region name [None]: ap-southeast-1
# Default output format [None]: json
```

3. Verify:
```bash
aws sts get-caller-identity
# Returns: UserId, Account, Arn
```
![Illustration](/images/IAM5.png)

## 4.3 Install AWS CDK & verify
```bash
# Install CDK globally
npm install -g aws-cdk

# Verify version
cdk --version
```
![Illustration](/images/IAM6.png)

## 4.4 CDK bootstrap (prepare the environment)

Run `cdk bootstrap` at least once per account/region so CDK can create the S3 bucket and other resources needed for deployment.

```bash
# Set region if desired
set AWS_REGION=ap-southeast-1

# Bootstrap environment (default)
cdk bootstrap aws://%ACCOUNT_ID%/%AWS_REGION%
```
![Illustration](/images/IAM7.png)
