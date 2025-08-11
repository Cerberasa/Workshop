---
title: "6. Deploy Application to EC2 ARM & x86"
weight: 6
draft: false
---

## Objective
Deploy the multi-architecture image from ECR and run the container on two EC2 instances (ARM Graviton and x86).  
Includes: manual run, handling port conflicts, checking the container, opening Security Group ports, and keeping the container running stably.

---

## 6.1 Preparation on both EC2s
- Docker installed (Amazon Linux 2 or Ubuntu).
- EC2 has outbound internet access or ECR access via VPC endpoint.
- EC2 has an IAM role allowing pull from ECR (`ecr:GetAuthorizationToken`, `ecr:BatchGetImage`, ...).
- Ensure **Security Group** opens port 80 or your desired public port (e.g., 8080).

---

## 6.2 Environment variables (on EC2 or local)
```bash
export AWS_REGION=ap-southeast-1
export ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
export ECR_REPO="$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/multiarch-demo"
export IMAGE_TAG="latest"
```

---

## 6.3 Login to ECR
```bash
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

---

## 6.4 Pull & run container

### 1. Stop old container if exists
```bash
docker ps
docker stop myapp || true
docker rm myapp || true
```

### 2. Pull image
```bash
docker pull $ECR_REPO:$IMAGE_TAG
```

### 3. Run container
```bash
docker run -d --restart unless-stopped --name myapp -p 80:8080 $ECR_REPO:$IMAGE_TAG
```

### 4. If port is already in use error occurs
```
docker: Error response from daemon: driver failed programming external connectivity on endpoint ... : Bind for 0.0.0.0:8080 failed: port is already allocated.
```
=> How to fix:
```bash
sudo netstat -tulpn | grep 8080       # Check process using the port
docker stop <container_id>
docker rm <container_id>
# Or change port mapping
docker run -d --restart unless-stopped --name myapp -p 8081:8080 $ECR_REPO:$IMAGE_TAG
```

---

## 6.5 Check container
```bash
docker ps
docker logs myapp --tail 50
docker exec -it myapp uname -m   # Check architecture: aarch64 or x86_64
```

---

## 6.6 Open port on Security Group
1. Go to AWS Console → EC2 → Security Groups of the instance.
2. Add inbound rule:  
   - Type: HTTP (or Custom TCP)  
   - Port: 80 or 8080  
   - Source: 0.0.0.0/0 (or your allowed IP)  
3. Save and try accessing:  
```bash
curl http://<EC2_PUBLIC_IP>:80
```

---

## 6.7 Deploy in parallel on ARM & x86
- Run the above steps sequentially on each EC2.

Example:
```bash
INSTANCE_IDS="i-x86 i-arm"
aws ssm send-command --instance-ids $INSTANCE_IDS --document-name "AWS-RunShellScript" --comment "Deploy multiarch" --parameters commands="aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com && docker pull $ECR_REPO:$IMAGE_TAG && docker stop myapp || true && docker rm myapp || true && docker run -d --restart unless-stopped --name myapp -p 80:8080 $ECR_REPO:$IMAGE_TAG"
```
