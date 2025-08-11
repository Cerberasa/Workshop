---
title: "6. Deploy ứng dụng lên EC2 ARM & x86"
weight: 6
draft: false
---

## Mục tiêu
Triển khai image multi-architecture từ ECR và chạy container trên hai EC2 instances (ARM Graviton và x86).  
Bao gồm: chạy thủ công, xử lý lỗi port, kiểm tra container, mở port Security Group, và giữ container chạy ổn định.

---

## 6.1 Chuẩn bị trên cả 2 EC2
- Đã cài Docker (Amazon Linux 2 hoặc Ubuntu).
- EC2 có outbound internet hoặc quyền truy cập ECR qua VPC endpoint.
- EC2 có IAM role cho phép pull từ ECR (`ecr:GetAuthorizationToken`, `ecr:BatchGetImage`, ...).
- Đảm bảo **Security Group** mở port 80 hoặc port bạn muốn public (ví dụ 8080).

---

## 6.2 Biến môi trường (trên EC2 hoặc local)
```bash
export AWS_REGION=ap-southeast-1
export ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
export ECR_REPO="$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/multiarch-demo"
export IMAGE_TAG="latest"
```

---

## 6.3 Đăng nhập ECR
```bash
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

---

## 6.4 Pull & chạy container

### 1. Dừng container cũ nếu có
```bash
docker ps
docker stop myapp || true
docker rm myapp || true
```

### 2. Pull image
```bash
docker pull $ECR_REPO:$IMAGE_TAG
```

### 3. Chạy container
```bash
docker run -d --restart unless-stopped --name myapp -p 80:8080 $ECR_REPO:$IMAGE_TAG
```

### 4. Nếu bị lỗi port đã dùng
```
docker: Error response from daemon: driver failed programming external connectivity on endpoint ... : Bind for 0.0.0.0:8080 failed: port is already allocated.
```
=> Cách xử lý:
```bash
sudo netstat -tulpn | grep 8080       # Xem tiến trình chiếm port
docker stop <container_id>
docker rm <container_id>
# Hoặc đổi port mapping
docker run -d --restart unless-stopped --name myapp -p 8081:8080 $ECR_REPO:$IMAGE_TAG
```

---

## 6.5 Kiểm tra container
```bash
docker ps
docker logs myapp --tail 50
docker exec -it myapp uname -m   # Kiểm tra kiến trúc: aarch64 hoặc x86_64
```

---

## 6.6 Mở port trên Security Group
1. Vào AWS Console → EC2 → Security Groups của instance.
2. Thêm inbound rule:  
   - Type: HTTP (hoặc Custom TCP)
   - Port: 80 hoặc 8080
   - Source: 0.0.0.0/0 (hoặc IP bạn muốn cho phép)
3. Lưu và thử truy cập:  
```bash
curl http://<EC2_PUBLIC_IP>:80
```

---

## 6.7 Deploy song song trên ARM & x86
- Chạy các bước trên lần lượt trên mỗi EC2.

Ví dụ:
```bash
INSTANCE_IDS="i-x86 i-arm"
aws ssm send-command --instance-ids $INSTANCE_IDS --document-name "AWS-RunShellScript" --comment "Deploy multiarch" --parameters commands="aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com && docker pull $ECR_REPO:$IMAGE_TAG && docker stop myapp || true && docker rm myapp || true && docker run -d --restart unless-stopped --name myapp -p 80:8080 $ECR_REPO:$IMAGE_TAG"
```



