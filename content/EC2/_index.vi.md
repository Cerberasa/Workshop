---
title: "2. Chuẩn bị môi trường AWS (EC2 ARM & x86)"
weight: 2
chapter: false
---

## Mục tiêu
Tạo hai máy chủ EC2 với kiến trúc **ARM** và **x86** để phục vụ việc triển khai và kiểm thử.  
Bao gồm cả việc cấu hình **VPC** và **Subnet** nếu chưa có sẵn.

---

## 2.1 Truy cập AWS Management Console
- Mở trình duyệt và truy cập: [https://aws.amazon.com/console/](https://aws.amazon.com/console/)
- Đăng nhập bằng tài khoản AWS của bạn.

---

## 2.2 Kiểm tra hoặc tạo mới VPC & Subnet

### 2.2.1 Kiểm tra VPC hiện có
1. Trong AWS Console, vào dịch vụ **VPC**: [https://console.aws.amazon.com/vpc/](https://console.aws.amazon.com/vpc/)
2. Chọn **Your VPCs** ở menu bên trái.
3. Nếu đã có VPC **default** → có thể dùng luôn.
4. Nếu muốn tạo mới → làm theo bước **2.2.2**.

### 2.2.2 Tạo VPC mới
1. Nhấn **Create VPC**.![Minh họa VPC](/images/create-vpc.png)
2. Chọn **VPC only**.
3. Đặt tên (VD: `workshop-vpc`).
4. IPv4 CIDR block: `10.0.0.0/16`
5. Giữ nguyên các tuỳ chọn khác → **Create VPC**.
![Minh họa VPC](/images/detail-vpc.png)

### 2.2.3 Tạo Subnet cho VPC mới
1. Vào mục **Subnets** → **Create subnet**.
2. Chọn VPC vừa tạo (`workshop-vpc`).
3. Đặt tên subnet (VD: `workshop-subnet-public`).
4. Chọn Availability Zone (VD: `ap-southeast-1a`).
5. IPv4 CIDR block: `10.0.1.0/24`
6. **Create subnet**.
![Minh họa Subner](/images/create-subnet.png)
7. Chỉnh Subnet sang chế độ **Auto-assign Public IPv4**:
   - Chọn subnet → **Actions → Edit subnet settings**
   - Tick vào **Enable auto-assign public IPv4 address** → **Save**.
![Minh họa Subner](/images/auto-subnet.png)

---

## 2.3 Tạo EC2 ARM (AWS Graviton)

1. Vào **EC2 → Launch Instance**: [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)
2. **Name**: `ec2-arm`
3. Chọn AMI: **Amazon Linux 2 ARM 64-bit (Graviton)**.
4. Chọn Instance Type: `t4g.nano` ![Minh họa Subner](/images/ec2-arm.png)
5. **Key pair**: Chọn hoặc tạo mới.
6. **Network settings**:
   - VPC: Chọn `workshop-vpc` (hoặc VPC mặc định nếu dùng default).
   - Subnet: Chọn `workshop-subnet-public` (hoặc subnet default).
   - Auto-assign Public IP: **Enable**.
   - Security Group: Tạo mới hoặc dùng sẵn, mở **port 22 (SSH)**.
7. Nhấn **Launch Instance**.
![Minh họa EC2 ARM](/images/arm-setting.png)

---

## 2.4 Tạo EC2 x86

1. Vào **EC2 → Launch Instance**.
2. **Name**: `ec2-x86`
3. Chọn AMI: **Amazon Linux 2 x86 64-bit**.
4. Chọn Instance Type: `t3.micro`![Minh họa EC2 x86](/images/ec2-x86.png)
5. **Key pair**: Chọn hoặc tạo mới.
6. **Network settings**:
   - VPC: Chọn `workshop-vpc` .
   - Subnet: Chọn `workshop-subnet-public`.
   - Auto-assign Public IP: **Enable**.
   - Security Group: Mở **port 22 (SSH)**.
7. Nhấn **Launch Instance**.![Minh họa EC2 x86](/images/x86-setting.png)




---

## 2.5 Kết quả
Bạn đã có 2 máy chủ:
- **EC2 ARM**: phục vụ kiểm thử kiến trúc ARM.
- **EC2 x86**: phục vụ kiểm thử kiến trúc x86.
![Minh họa EC2 ](/images/ec2-kq.png)

## 2.6 Tạo ECS Cluster thủ công trên AWS Console

1. Vào dịch vụ **Amazon ECS**: [https://console.aws.amazon.com/ecs/](https://console.aws.amazon.com/ecs/).
2. Nhấn **Create Cluster**.
3. **Cluster name**: nhập tên (VD: `MyManualEC2Cluster`).
4. Ở phần **Infrastructure**:
   - Chọn **Amazon Fargate(serverless)**.
5. Các tuỳ chọn khác để mặc định → **Create**.

---

## 2.7 Cài ECS Agent và gắn EC2 vào Cluster

Lặp lại các bước sau cho cả EC2 ARM và EC2 x86.

**Bước 1: SSH vào EC2**
```bash
ssh -i your-key.pem ec2-user@<Public_IP>
```

**Bước 2: Cài ECS Agent**
```bash
sudo amazon-linux-extras enable ecs
sudo yum install -y ecs-init
```

**Bước 3: Cấu hình tên Cluster**
```bash
echo "ECS_CLUSTER=MyManualEC2Cluster" | sudo tee -a /etc/ecs/ecs.config
```
> Thay `MyManualEC2Cluster` bằng tên cluster bạn đã tạo.

**Bước 4: Khởi động ECS Agent**
```bash
sudo systemctl enable --now ecs
```

---

## 2.8 Kiểm tra EC2 đã join cluster
- Vào AWS Console → ECS → Clusters → chọn `MyManualEC2Cluster` → tab **ECS Instances**.
- Bạn sẽ thấy 2 EC2 (ARM và x86) xuất hiện nếu ECS Agent chạy thành công.

**Lưu ý**:
- EC2 phải gắn IAM Role có quyền **AmazonEC2ContainerServiceforEC2Role**.
- Nếu chưa có:
  1. Tạo role mới với policy `AmazonEC2ContainerServiceforEC2Role`.
  2. Gắn role đó vào EC2 qua **Actions → Security → Modify IAM Role**.
- Security Group của EC2 phải mở port 22 (SSH) và các port dịch vụ bạn muốn chạy.


![Minh họa EC2 x86](/images/Cluster.png)
