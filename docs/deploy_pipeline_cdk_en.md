---
title: "5. Deploy the pipeline with CDK"
weight: 5
draft: false
---

## Objective
Deploy the CDK stack to create the AWS services required for the pipeline, including:
- **CodePipeline** (automated build & deploy)
- **CodeBuild** (build multi-architecture images)
- **ECR (Elastic Container Registry)** (store images)
- **IAM Roles & Policies** (access for pipeline and build)
- **S3 Bucket** (store pipeline artifacts)
- **KMS Key** (encrypt the S3 bucket)
- **ECS Cluster** (run containers)
- **CloudWatch Logs** (store build and ECS logs)
- **Security Groups** (open ports for ECS if needed)
- **CloudFormation Stacks** (manage the entire infrastructure)

---

## 5.1 Set environment variables
On **Windows PowerShell**:
```powershell
$env:CODESTAR_CONNECTION_ARN="arn:aws:codestar-connections:ap-southeast-1:<account_id>:connection/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$env:AWS_REGION="ap-southeast-1"
```

On **macOS / Linux**:
```bash
export CODESTAR_CONNECTION_ARN="arn:aws:codestar-connections:ap-southeast-1:<account_id>:connection/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
export AWS_REGION="ap-southeast-1"
```
---

## 5.2 Check synth before deploy
Change into the `cdk` directory of the repo and install dependencies if needed:
```bash
cd aws-multiarch-container-build-pipeline
# if TypeScript
npm install

# or Python
# python3 -m venv .venv
# source .venv/bin/activate
# pip install -r requirements.txt
```

Run synth to validate the CloudFormation template:
```bash
cdk synth
```
If `cdk synth` succeeds, continue to deploy.

---

## 5.3 Run deploy
```bash
# from the cdk directory
cdk deploy MultiArchECSPipelineDemo
```
- CDK will show changes and prompt for confirmation (y/n).
- The pipeline will automatically connect to GitHub via the CodeStar Connection you created.
- **Note**: During deployment, AWS will create **all services listed in section 4.5**, excluding ALB and EC2 (which were removed), so the cost will be lower.

---

## 5.4 Post-deploy checks

- **CodePipeline** → check the pipeline and stage statuses.
- **CodeBuild** → check the project and logs (if the pipeline has triggered a build).
- **ECR** → check the repository (after the first build, images will appear).
- **ECS** → check the cluster & service.
- **S3 Bucket** → view pipeline artifacts.
- **KMS Key** → verify encryption.
