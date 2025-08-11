---
title : "Backup & Disaster Recovery"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---

# Làm việc với AWS Backup & Disaster Recovery Automation

### Tổng quan

Trong bài lab này, bạn sẽ tìm hiểu các khái niệm cơ bản và thực hành về **AWS Backup** và **Disaster Recovery** cho môi trường **multi-database** (DynamoDB, DocumentDB,...).  
Bài lab bao gồm việc tạo kế hoạch sao lưu tự động, lưu trữ bản sao an toàn, kiểm thử khả năng phục hồi dữ liệu và thiết lập thông báo khi có sự cố.

![VPC](/images/2.prerequisite/anhdiagram.png)

### Nội dung

1. [Giới thiệu dự án](1-introduce/)
2. [Các bước chuẩn bị](2-prerequisite/)
3. [Tạo và cấu hình kế hoạch Backup](3-create-backup-plan/)
4. [Tích hợp Lambda Functions để kiểm thử & gửi thông báo](4-lambda-functions/)
5. [Dọn dẹp tài nguyên](5-cleanup/)