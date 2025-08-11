---
title: "7. Cleanup resources"
weight: 6
draft: false
---

## Objective
Quickly and safely clean up the resources created for the workshop to avoid unexpected costs.

> Before deleting: **backup** any important reports / logs. Confirm the correct account and region.

---

## 7.1 Check account & region
```bash
aws sts get-caller-identity
aws configure get region
```

---

## 7.2 Destroy with **CDK**
```bash
cd aws-multiarch-container-build-pipeline/example/ecs-pipeline
cdk list
cdk destroy --all
```
- CDK will delete resources following dependency order.
- If you encounter errors (for example an S3 bucket not empty), go to the CloudFormation Console -> Events to see details.
---
![Illustration](/images/For.png)

## 7.3 Stop / terminate EC2 instances
```bash
```
![Illustration](/images/ClEC2.png)

## 7.3 Delete S3 buckets
```bash
```
![Illustration](/images/CLS3.png)
![Illustration](/images/CLDS3.png)

## 7.3 Delete ECR repositories
```bash
```
![Illustration](/images/CLECR.png)

## 7.3 Delete IAM roles
```bash
```
![Illustration](/images/CLROLE.png)
