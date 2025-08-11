---
title : "Chuẩn bị môi trường AWS "
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1.1 </b> "
---


# 🚀 Hướng dẫn tạo EC2 Instance trên AWS

## 1. Đăng nhập AWS Console
- Truy cập [https://console.aws.amazon.com/](https://console.aws.amazon.com/)
- Chọn **EC2** từ menu dịch vụ.

---

## 2. Tạo EC2 Instance
1. **Bấm nút** `Launch instance`.
2. **Đặt tên**: ví dụ `EC2-DynamoDB-Workshop`.
3. **Chọn Amazon Machine Image (AMI)**:
   - `Amazon Linux 2 AMI (HVM), SSD Volume Type`.
4. **Chọn Instance Type**:
   - `t2.micro` (Free Tier).
5. **Tạo hoặc chọn Key Pair**:
   - Nếu chưa có: chọn **Create new key pair**, tải file `.pem` về.
6. **Cấu hình Network**:
   - VPC: Chọn VPC mặc định hoặc VPC riêng.
   - Subnet: Chọn subnet theo vùng.
   - Enable Auto-assign Public IP: **Yes**.
7. **Security Group**:
   - Tạo mới hoặc chọn Security Group có rule:
     - `SSH` (Port 22) - Your IP.
     - (Tuỳ dự án) mở thêm HTTP (Port 80), HTTPS (Port 443).
8. **Storage**:
   - 8 GiB (SSD GP2) là đủ cho workshop.
9. **Bấm** `Launch Instance`.
![VPC](/images/2.prerequisite/EC2.png)
---

## 3. Kết nối tới EC2


1. Vào **EC2 Dashboard** → Chọn instance vừa tạo.
2. Bấm **Connect** → Tab **SSH client**.
3. Chạy trên máy local:
```bash
chmod 400 your-key.pem
ssh -i "your-key.pem" ec2-user@<Public-IP>

```
![VPC](/images/2.prerequisite/1.png)