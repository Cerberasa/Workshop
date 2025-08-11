---
title: " 1.Giới thiệu" 
date: "2025-08-06"
weight: 1
---

#### Tổng quan
Trong bối cảnh hạ tầng điện toán đám mây phát triển nhanh chóng, các doanh nghiệp và đội ngũ phát triển phần mềm ngày càng quan tâm đến việc tối ưu **hiệu năng**, **chi phí vận hành**, và **tính bền vững về năng lượng**.  
Hai kiến trúc phần cứng chủ đạo hiện nay trên AWS là:

- **x86** (Intel/AMD): vốn là tiêu chuẩn truyền thống, có hệ sinh thái phần mềm rộng, khả năng tương thích tốt với ứng dụng legacy.
- **ARM** (AWS Graviton): sử dụng tập lệnh RISC, tối ưu cho hiệu suất trên mỗi watt, tiêu thụ điện năng thấp hơn và chi phí vận hành thấp hơn tới 40% so với x86 trong nhiều trường hợp.

Tuy nhiên, triển khai và quản lý ứng dụng cho cả hai kiến trúc cùng lúc thường gặp khó khăn:  
- Cần build image riêng cho mỗi kiến trúc.  
- Cần môi trường kiểm thử đa kiến trúc.  
- Cần pipeline tự động để tránh lỗi thủ công và tiết kiệm thời gian.

Workshop này hướng dẫn bạn xây dựng **CI/CD pipeline multi-architecture** để triển khai ứng dụng mẫu trên cả ARM và x86 một cách tự động
Kết quả thu được sẽ giúp bạn đưa ra quyết định **chuyển đổi workload sang ARM** hay **duy trì trên x86** dựa trên dữ liệu thực nghiệm thay vì giả định.

---

## Hệ thống bao gồm các thành phần chính:

- **Amazon EC2 (t4g.micro – ARM, t3.micro – x86)**: cung cấp máy chủ ảo trên hai kiến trúc khác nhau để triển khai và kiểm thử ứng dụng.
- **Amazon ECR (Elastic Container Registry)**: lưu trữ Docker image đa kiến trúc (multi-arch) để dễ dàng phân phối cho cả ARM và x86.
- **AWS IAM (Identity and Access Management)**: quản lý quyền truy cập và bảo mật cho pipeline, ECR và EC2.
- **AWS VPC (Virtual Private Cloud)**: cung cấp mạng riêng ảo, bảo vệ môi trường triển khai và kiểm thử.
- **AWS CodePipeline / CodeBuild**: dịch vụ CI/CD native của AWS để tự động build, test và deploy image đa kiến trúc.
- **AWS ECR Multi-Arch Build Support**: thay thế Docker Buildx, sử dụng dịch vụ AWS native để build image cho ARM và x86 từ cùng một codebase.

---

## Hệ thống được thiết kế để:

- **Tiết kiệm chi phí** vận hành tới 40% khi sử dụng ARM cho workload phù hợp.
- **Tự động hóa quy trình build và deploy** cho cả hai kiến trúc, giảm lỗi thủ công và tiết kiệm thời gian.
- **Đảm bảo tính tương thích** thông qua kiểm thử đa kiến trúc trước khi đưa vào môi trường production.
- **So sánh chi phí và năng lượng tiêu thụ** để tối ưu chiến lược triển khai.

---

![Multi-Arch Diagram](/images/diagram.png?featherlight=false&width=90pc)

{{% notice note %}}
Multi-architecture deployment giúp bạn tận dụng tối đa ưu điểm của từng nền tảng phần cứng. ARM phù hợp với workload tối ưu hóa chi phí và tiết kiệm năng lượng, trong khi x86 đảm bảo tính tương thích với phần mềm legacy và môi trường đã ổn định.
{{% /notice %}}

---

## Nội dung chính

1. [Giới thiệu](/vi/introduction)
2. [Chuẩn bị môi trường AWS (EC2 ARM & x86)](/vi/ec2/)
3. [Chuẩn bị repository mã nguồn và Clone repo mẫu](/vi/clone/)
4. [Cài đặt công cụ và cấu hình AWS CLI, CDK](/vi/cli-cdk/)
5. [Triển khai pipeline bằng CDK](/vi/cdk-pipeline/)
6. [Deploy ứng dụng lên ARM và x86 EC2](/vi/deploy/)
7. [Dọn dẹp tài nguyên](/vi/cleanup/)
