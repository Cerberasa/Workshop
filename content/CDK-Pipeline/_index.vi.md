---
title: "5. Triển khai pipeline bằng CDK"
weight: 5
draft: false
---

## Mục tiêu
Triển khai stack CDK để tạo các dịch vụ AWS cần thiết cho pipeline, bao gồm:
- **CodePipeline** (tự động build & deploy)
- **CodeBuild** (build image đa kiến trúc)
- **ECR (Elastic Container Registry)** (lưu trữ image)
- **IAM Roles & Policies** (quyền truy cập cho pipeline và build)
- **S3 Bucket** (lưu artifact của pipeline)
- **KMS Key** (mã hóa S3 bucket)
- **ECS Cluster** (chạy container)
- **CloudWatch Logs** (lưu log build và ECS)
- **Security Groups** (mở port cho ECS nếu cần)
- **CloudFormation Stacks** (quản lý toàn bộ hạ tầng)

---


---

## 5.1 Thiết lập biến môi trường
Trên **Windows PowerShell**:
```powershell
$env:CODESTAR_CONNECTION_ARN="arn:aws:codestar-connections:ap-southeast-1:<account_id>:connection/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$env:AWS_REGION="ap-southeast-1"
```

Trên **macOS / Linux**:
```bash
export CODESTAR_CONNECTION_ARN="arn:aws:codestar-connections:ap-southeast-1:<account_id>:connection/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
export AWS_REGION="ap-southeast-1"
```
---

## 5.2 Kiểm tra synth trước khi deploy
Chuyển vào thư mục `cdk` của repo và cài dependencies nếu cần:
```bash
cd aws-multiarch-container-build-pipeline
# nếu TypeScript
npm install

# hoặc Python
# python3 -m venv .venv
# source .venv/bin/activate
# pip install -r requirements.txt
```

Chạy synth để kiểm tra CloudFormation template:
```bash
cdk synth
```
Nếu `cdk synth` thành công, tiếp tục deploy.

---

## 5.3 Chạy deploy
```bash
# từ thư mục cdk
cdk deploy MultiArchECSPipelineDemo
```
- CDK sẽ hiển thị các thay đổi và hỏi xác nhận (y/n).
- Pipeline sẽ tự động kết nối tới GitHub qua CodeStar Connection mà bạn đã tạo.
- **Lưu ý**: Khi deploy, AWS sẽ tạo **tất cả dịch vụ đã liệt kê ở mục 4.5**, không bao gồm ALB và EC2 (vì đã bỏ), nên chi phí thấp hơn.

---

## 5.4 Kiểm tra sau deploy

- **CodePipeline** → kiểm tra pipeline, trạng thái từng stage.
- **CodeBuild** → kiểm tra project và logs (nếu pipeline đã chạy build).
- **ECR** → kiểm tra repository (sau khi build lần đầu, image sẽ xuất hiện).
- **ECS** → kiểm tra cluster & service.
- **S3 Bucket** → xem artifact pipeline.
- **KMS Key** → kiểm tra mã hóa dữ liệu.
