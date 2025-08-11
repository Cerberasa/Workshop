---
title: "2. Prepare AWS Environment (EC2 ARM & x86)"
weight: 2
chapter: false
---

## Objective
Create two EC2 instances with **ARM** and **x86** architectures to serve deployment and testing purposes.  
Includes configuring **VPC** and **Subnet** if not already available.

---

## 2.1 Access AWS Management Console
- Open your browser and go to: [https://aws.amazon.com/console/](https://aws.amazon.com/console/)
- Log in with your AWS account.

---

## 2.2 Check or create new VPC & Subnet

### 2.2.1 Check existing VPC
1. In AWS Console, go to the **VPC** service: [https://console.aws.amazon.com/vpc/](https://console.aws.amazon.com/vpc/)
2. Select **Your VPCs** from the left menu.
3. If a **default** VPC exists → you can use it directly.
4. If you want to create a new one → follow step **2.2.2**.

### 2.2.2 Create a new VPC
1. Click **Create VPC**. ![VPC Illustration](/images/create-vpc.png)
2. Select **VPC only**.
3. Name it (e.g., `workshop-vpc`).
4. IPv4 CIDR block: `10.0.0.0/16`
5. Keep other options as default → **Create VPC**.
![VPC Illustration](/images/detail-vpc.png)

### 2.2.3 Create Subnet for new VPC
1. Go to **Subnets** → **Create subnet**.
2. Select the VPC you just created (`workshop-vpc`).
3. Name the subnet (e.g., `workshop-subnet-public`).
4. Choose Availability Zone (e.g., `ap-southeast-1a`).
5. IPv4 CIDR block: `10.0.1.0/24`
6. **Create subnet**.
![Subnet Illustration](/images/create-subnet.png)
7. Change the Subnet to **Auto-assign Public IPv4** mode:
   - Select subnet → **Actions → Edit subnet settings**
   - Tick **Enable auto-assign public IPv4 address** → **Save**.
![Subnet Illustration](/images/auto-subnet.png)

---

## 2.3 Create EC2 ARM (AWS Graviton)

1. Go to **EC2 → Launch Instance**: [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)
2. **Name**: `ec2-arm`
3. Select AMI: **Amazon Linux 2 ARM 64-bit (Graviton)**.
4. Select Instance Type: `t4g.nano` ![Subnet Illustration](/images/ec2-arm.png)
5. **Key pair**: Choose or create a new one.
6. **Network settings**:
   - VPC: Select `workshop-vpc` (or default VPC if using default).
   - Subnet: Select `workshop-subnet-public` (or default subnet).
   - Auto-assign Public IP: **Enable**.
   - Security Group: Create new or reuse, open **port 22 (SSH)**.
7. Click **Launch Instance**.
![EC2 ARM Illustration](/images/arm-setting.png)

---

## 2.4 Create EC2 x86

1. Go to **EC2 → Launch Instance**.
2. **Name**: `ec2-x86`
3. Select AMI: **Amazon Linux 2 x86 64-bit**.
4. Select Instance Type: `t3.micro` ![EC2 x86 Illustration](/images/ec2-x86.png)
5. **Key pair**: Choose or create a new one.
6. **Network settings**:
   - VPC: Select `workshop-vpc`.
   - Subnet: Select `workshop-subnet-public`.
   - Auto-assign Public IP: **Enable**.
   - Security Group: Open **port 22 (SSH)**.
7. Click **Launch Instance**. ![EC2 x86 Illustration](/images/x86-setting.png)

---

## 2.5 Result
You now have 2 instances:
- **EC2 ARM**: for ARM architecture testing.
- **EC2 x86**: for x86 architecture testing.
![EC2 Illustration](/images/ec2-kq.png)

## 2.6 Manually create ECS Cluster on AWS Console

1. Go to **Amazon ECS**: [https://console.aws.amazon.com/ecs/](https://console.aws.amazon.com/ecs/).
2. Click **Create Cluster**.
3. **Cluster name**: enter a name (e.g., `MyManualEC2Cluster`).
4. In the **Infrastructure** section:
   - Select **Amazon Fargate (serverless)**.
5. Leave other options as default → **Create**.

---

## 2.7 Install ECS Agent and register EC2 to Cluster

Repeat the following steps for both EC2 ARM and EC2 x86.

**Step 1: SSH into EC2**
```bash
ssh -i your-key.pem ec2-user@<Public_IP>
```

**Step 2: Install ECS Agent**
```bash
sudo amazon-linux-extras enable ecs
sudo yum install -y ecs-init
```

**Step 3: Configure cluster name**
```bash
echo "ECS_CLUSTER=MyManualEC2Cluster" | sudo tee -a /etc/ecs/ecs.config
```
> Replace `MyManualEC2Cluster` with the cluster name you created.

**Step 4: Start ECS Agent**
```bash
sudo systemctl enable --now ecs
```

---

## 2.8 Verify EC2 joined the cluster
- Go to AWS Console → ECS → Clusters → select `MyManualEC2Cluster` → **ECS Instances** tab.
- You will see 2 EC2 instances (ARM and x86) if the ECS Agent is running successfully.

**Notes**:
- EC2 must be attached to an IAM Role with **AmazonEC2ContainerServiceforEC2Role** permission.
- If not available:
  1. Create a new role with the `AmazonEC2ContainerServiceforEC2Role` policy.
  2. Attach the role to EC2 via **Actions → Security → Modify IAM Role**.
- The EC2 Security Group must open port 22 (SSH) and the service ports you want to run.

![EC2 x86 Illustration](/images/Cluster.png)
