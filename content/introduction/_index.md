---
title: " 1.Introduction"
date: "2025-08-06"
weight: 1
---

#### Overview
In the context of rapidly developing cloud computing infrastructure, businesses and software development teams are increasingly focused on optimizing **performance**, **operating costs**, and **energy sustainability**.  
The two main hardware architectures currently on AWS are:

- **x86** (Intel/AMD): the traditional standard, with a broad software ecosystem and strong compatibility with legacy applications.  
- **ARM** (AWS Graviton): uses the RISC instruction set, optimized for performance per watt, consumes less power, and can reduce operating costs by up to 40% compared to x86 in many cases.

However, deploying and managing applications for both architectures at the same time often comes with challenges:  
- Requires separate image builds for each architecture.  
- Requires multi-architecture testing environments.  
- Requires automated pipelines to avoid manual errors and save time.

This workshop will guide you in building a **multi-architecture CI/CD pipeline** to deploy a sample application on both ARM and x86 automatically.  
The outcome will help you decide whether to **migrate workloads to ARM** or **stay on x86** based on real experimental data rather than assumptions.

---

## The system consists of the following main components:

- **Amazon EC2 (t4g.micro – ARM, t3.micro – x86)**: provides virtual servers on two different architectures for deploying and testing applications.  
- **Amazon ECR (Elastic Container Registry)**: stores multi-architecture Docker images for easy distribution to both ARM and x86.  
- **AWS IAM (Identity and Access Management)**: manages access control and security for the pipeline, ECR, and EC2.  
- **AWS VPC (Virtual Private Cloud)**: provides a private network, securing the deployment and testing environment.  
- **AWS CodePipeline / CodeBuild**: AWS native CI/CD services for automatically building, testing, and deploying multi-architecture images.  
- **AWS ECR Multi-Arch Build Support**: replaces Docker Buildx, using native AWS services to build images for both ARM and x86 from the same codebase.

---

## The system is designed to:

- **Save up to 40% in operating costs** when using ARM for suitable workloads.  
- **Automate the build and deployment process** for both architectures, reducing manual errors and saving time.  
- **Ensure compatibility** through multi-architecture testing before moving to production.  
- **Compare costs and energy consumption** to optimize deployment strategy.

---

![Multi-Arch Diagram](/images/diagram.png?featherlight=false&width=90pc)

{{% notice note %}}  
Multi-architecture deployment allows you to fully leverage the strengths of each hardware platform. ARM is suitable for workloads optimized for cost and energy efficiency, while x86 ensures compatibility with legacy software and stable environments.  
{{% /notice %}}

---

## Main contents

1. [Introduction](/introduction)  
2. [Prepare AWS environment (EC2 ARM & x86)](/ec2/)  
3. [Prepare source code repository and clone sample repo](/clone/)  
4. [Install tools and configure AWS CLI, CDK](/cli-cdk/)  
5. [Deploy pipeline using CDK](/cdk-pipeline/)  
6. [Deploy application to ARM and x86 EC2](/deploy/)  
7. [Clean up resources](/cleanup/)  
