---
title : "Tạo Public subnet"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---


## 1. Cài đặt AWS CLI trên EC2 (Amazon Linux 2)
````markdown
# 🚀 Hướng dẫn tạo các bảng DynamoDB trên EC2 bằng AWS CLI

```bash
sudo yum update -y
sudo yum install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
````

---

## 2. Đăng nhập AWS CLI

```bash
aws configure
```

Nhập:

* **AWS Access Key ID**: (Lấy từ IAM)
* **AWS Secret Access Key**: (Lấy từ IAM)
![VPC](/images/2.prerequisite/2.22.png)
* **Default region name**: `ap-southeast-1`
* **Default output format**: `json`

> 🔹 Yêu cầu IAM User có quyền `dynamodb:CreateTable`, `dynamodb:PutItem`, `dynamodb:DescribeTable`, `dynamodb:ListTables`.
![VPC](/images/2.prerequisite/2.1.2.2.png)
---

## 3. Tạo file `create_tables.sh`

```bash
nano create_tables.sh
```

Dán nội dung sau:

```bash
#!/bin/bash

# ====== 1. CategoryHoiThao ======
aws dynamodb create-table \
    --table-name CategoryHoiThao \
    --attribute-definitions AttributeName=categoryId,AttributeType=S \
    --key-schema AttributeName=categoryId,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name CategoryHoiThao \
    --item '{"categoryId": {"S": "C001"}, "name": {"S": "Hội thảo công nghệ"}}'


# ====== 2. HistoryBook ======
aws dynamodb create-table \
    --table-name HistoryBook \
    --attribute-definitions \
        AttributeName=bookingId,AttributeType=S \
        AttributeName=eventId,AttributeType=S \
    --key-schema \
        AttributeName=bookingId,KeyType=HASH \
        AttributeName=eventId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name HistoryBook \
    --item '{"bookingId": {"S": "B001"}, "eventId": {"S": "E001"}, "status": {"S": "confirmed"}}'


# ====== 3. HistoryBookUsers ======
aws dynamodb create-table \
    --table-name HistoryBookUsers \
    --attribute-definitions \
        AttributeName=userId,AttributeType=S \
        AttributeName=bookingId,AttributeType=S \
    --key-schema \
        AttributeName=userId,KeyType=HASH \
        AttributeName=bookingId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name HistoryBookUsers \
    --item '{"userId": {"S": "U001"}, "bookingId": {"S": "B001"}}'


# ====== 4. HoiThao ======
aws dynamodb create-table \
    --table-name HoiThao \
    --attribute-definitions AttributeName=eventId,AttributeType=S \
    --key-schema AttributeName=eventId,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name HoiThao \
    --item '{"eventId": {"S": "E001"}, "title": {"S": "Hội thảo AI"}}'


# ====== 5. Role ======
aws dynamodb create-table \
    --table-name Role \
    --attribute-definitions AttributeName=roleId,AttributeType=S \
    --key-schema AttributeName=roleId,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name Role \
    --item '{"roleId": {"S": "R001"}, "roleName": {"S": "Admin"}}'


# ====== 6. User ======
aws dynamodb create-table \
    --table-name User \
    --attribute-definitions AttributeName=userId,AttributeType=S \
    --key-schema AttributeName=userId,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name User \
    --item '{"userId": {"S": "U001"}, "userName": {"S": "Nguyen Van A"}}'


# ====== 7. UserGiaiDau ======
aws dynamodb create-table \
    --table-name UserGiaiDau \
    --attribute-definitions \
        AttributeName=userId,AttributeType=S \
        AttributeName=tournamentId,AttributeType=S \
    --key-schema \
        AttributeName=userId,KeyType=HASH \
        AttributeName=tournamentId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name UserGiaiDau \
    --item '{"userId": {"S": "U001"}, "tournamentId": {"S": "T001"}}'


# ====== 8. UserHoiThao ======
aws dynamodb create-table \
    --table-name UserHoiThao \
    --attribute-definitions \
        AttributeName=userId,AttributeType=S \
        AttributeName=eventId,AttributeType=S \
    --key-schema \
        AttributeName=userId,KeyType=HASH \
        AttributeName=eventId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name UserHoiThao \
    --item '{"userId": {"S": "U001"}, "eventId": {"S": "E001"}}'


# ====== 9. UserLiveShow ======
aws dynamodb create-table \
    --table-name UserLiveShow \
    --attribute-definitions \
        AttributeName=userId,AttributeType=S \
        AttributeName=liveShowId,AttributeType=S \
    --key-schema \
        AttributeName=userId,KeyType=HASH \
        AttributeName=liveShowId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name UserLiveShow \
    --item '{"userId": {"S": "U001"}, "liveShowId": {"S": "L001"}}'


# ====== 10. UserRole ======
aws dynamodb create-table \
    --table-name UserRole \
    --attribute-definitions \
        AttributeName=userId,AttributeType=S \
        AttributeName=roleId,AttributeType=S \
    --key-schema \
        AttributeName=userId,KeyType=HASH \
        AttributeName=roleId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST

aws dynamodb put-item \
    --table-name UserRole \
    --item '{"userId": {"S": "U001"}, "roleId": {"S": "R001"}}'

echo "✅ Tạo xong tất cả bảng và dữ liệu mẫu"
```
![VPC](/images/2.prerequisite/2.11.png)

---

## 4. Cấp quyền chạy & thực thi script

```bash
chmod +x create_tables.sh
./create_tables.sh
```

---

## 5. Kiểm tra kết quả

```bash
aws dynamodb list-tables
```

Nếu tạo thành công sẽ hiển thị:

```json
{
    "TableNames": [
        "CategoryHoiThao",
        "HistoryBook",
        "HistoryBookUsers",
        "HoiThao",
        "Role",
        "User",
        "UserGiaiDau",
        "UserHoiThao",
        "UserLiveShow",
        "UserRole"
    ]
}
```
![VPC](/images/2.prerequisite/2.1.2.4.png)
---

## 6. Mẹo xử lý lỗi

* Nếu gặp lỗi `AccessDeniedException`, kiểm tra IAM Policy của user, đảm bảo có quyền:

```json
{
    "Effect": "Allow",
    "Action": [
        "dynamodb:*"
    ],
    "Resource": "*"
}
```
