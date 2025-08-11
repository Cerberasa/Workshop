---
title: "7. Dọn dẹp tài nguyên"
weight: 6
draft: false
---

## Mục tiêu
Dọn dẹp nhanh và an toàn các tài nguyên đã tạo cho workshop, tránh phát sinh chi phí ngoài ý muốn.

> Trước khi xóa: **sao lưu** mọi báo cáo / logs quan trọng. Xác nhận account và region đúng.

---

## 7.1 Kiểm tra tài khoản & region
```bash
aws sts get-caller-identity
aws configure get region
```

---

## 7.2 triển khai bằng **CDK**
```bash
cd aws-multiarch-container-build-pipeline/example/ecs-pipeline
cdk list
cdk destroy --all
```
- CDK sẽ xóa resources theo đúng thứ tự dependency.
- Nếu gặp lỗi (ví dụ S3 bucket không rỗng), vào CloudFormation Console -> Events để xem chi tiết.
---
![Minh họa EC2 x86](/images/For.png)

## 7.3 Dừng / terminate EC2 instances
```bash
```
![Minh họa EC2 x86](/images/ClEC2.png)
## 7.3 Xóa S3 buckets
```bash
```
![Minh họa EC2 x86](/images/CLS3.png)
![Minh họa EC2 x86](/images/CLDS3.png)

## 7.3 Xóa ECR repository
```bash
```
![Minh họa EC2 x86](/images/CLECR.png)

## 7.3 Xóa IAM roles
```bash
```
![Minh họa EC2 x86](/images/CLROLE.png)


