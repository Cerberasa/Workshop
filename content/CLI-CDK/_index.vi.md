---
title: "4. Cài đặt công cụ và cấu hình AWS CLI, CDK"
weight: 4
draft: false
---

## Mục tiêu
Cài đặt AWS CLI, cấu hình credentials, cài Node.js/npm (nếu cần) và cài AWS CDK để chuẩn bị triển khai pipeline bằng CDK.

## 4.1 Cài đặt AWS CLI (AWS CLI v2) & kiểm tra

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
Tải MSI installer từ: https://aws.amazon.com/cli/  
Hoặc dùng Chocolatey:
```powershell
choco install awscli
```
![Minh họa](/images/download.png)
### Kiểm tra
```bash
aws --version
# ví dụ: aws-cli/2.x.x Python/3.x ...
```
![Minh họa](/images/version.png)
## 4.2 Cấu hình AWS credentials (aws configure)

> Gợi ý bảo mật: Tạo một IAM user riêng cho workshop với quyền programmatic access. Trong demo nhanh có thể dùng `AdministratorAccess`, nhưng trong production nên dùng least-privilege.

1. Tạo IAM User trên AWS Console [https://aws.amazon.com/console/](https://aws.amazon.com/console/) → **IAM** → **Users** → **Create user**  
   - Chọn **Programmatic access**  
![Minh họa](/images/IAM1.png)
   - Gán policy phù hợp 
![Minh họa](/images/IAM2.png)
![Minh họa](/images/IAM3.png)
   - Lưu **Access Key ID** và **Secret Access Key**
![Minh họa](/images/IAM4.png)
2. Trên máy local (cấu hình credentials):
```bash
aws configure
# Nhập lần lượt:
# AWS Access Key ID [None]: <ACCESS_KEY_ID>
# AWS Secret Access Key [None]: <SECRET_ACCESS_KEY>
# Default region name [None]: ap-southeast-1
# Default output format [None]: json
```

3. Kiểm tra:
```bash
aws sts get-caller-identity
# Trả về: UserId, Account, Arn
```
![Minh họa](/images/IAM5.png)

## 3.3 Cài AWS CDK & kiểm tra
```bash
# Cài CDK toàn cục
npm install -g aws-cdk

# Kiểm tra phiên bản
cdk --version
```
![Minh họa](/images/IAM6.png)

## 4.4 CDK bootstrap (chuẩn bị môi trường)

Chạy `cdk bootstrap` ít nhất một lần cho account/region để CDK tạo S3 bucket và các resources hỗ trợ deploy.

```bash
# Gán region nếu muốn
set AWS_REGION=ap-southeast-1

# Khởi tạo môi trường bootstrap (mặc định)
cdk bootstrap aws://%ACCOUNT_ID%/%AWS_REGION%
```
![Minh họa](/images/IAM7.png)
