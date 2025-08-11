---
title : "🚀 Triển khai AWS Backup Alert & Verification với Lambda và SNS"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---

## 1. Chuẩn bị SNS Topic (gửi thông báo Email/SMS)

### 1.1 Tạo SNS Topic

1.  Mở **AWS Console** → tìm **SNS** → **Topics** → **Create topic**.
2.  Chọn **Standard**.
3.  Name: `BackupAlertsTopic`.
4.  Nhấn **Create topic**.
![VPC](/images/2.prerequisite/4.1.1.1.png)

### 1.2 Tạo Subscription (Email/SMS)

1.  Trong SNS Topic vừa tạo → **Create subscription**.
2.  Protocol: `Email` (hoặc `SMS`).
3.  Endpoint: nhập email hoặc số điện thoại.
4.  Nhấn **Create subscription**.
5.  **Xác nhận** qua email (bắt buộc nếu chọn Email).
![VPC](/images/2.prerequisite/4.2.2.png)
![VPC](/images/2.prerequisite/4.2.3.png)


------------------------------------------------------------------------

## 2. Lambda Function 1 --- Backup Verification

### 2.1 Tạo Lambda

1.  Vào **AWS Console** → **Lambda** → **Create function**.
2.  **Author from scratch**.
3.  Function name: `BackupVerificationLambda`.
4.  Runtime: `Python 3.10`.
![VPC](/images/2.prerequisite/4.22.1.png)
5.  Role: chọn **Create a new role with basic Lambda permissions**.
6.  Nhấn **Create function**.
![VPC](/images/2.prerequisite/4.22.2.png)

### 2.2 Gán quyền cho Lambda

Vào **IAM Console** → chọn role của Lambda → **Add inline policy** →
**JSON**:
![VPC](/images/2.prerequisite/4.22.3.png)
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "backup:ListBackupJobs",
                "backup:DescribeBackupJob",
                "dynamodb:RestoreTableFromBackup",
                "rds:RestoreDBClusterFromSnapshot",
                "sns:Publish"
            ],
            "Resource": "*"
        }
    ]
}
```
![VPC](/images/2.prerequisite/4.22.33.png)
Lưu với tên `LambdaBackupPolicy`.
![VPC](/images/2.prerequisite/4.22.333.png)
### 2.3 Thêm biến môi trường

-   Key: `SNS_TOPIC_ARN`\
-   Value: ARN của SNS Topic vừa tạo.
![VPC](/images/2.prerequisite/4.222.3.png)

### 2.4 Code Python
- Deploy code:
``` python
import boto3
import os

backup_client = boto3.client('backup')
sns = boto3.client('sns')

SNS_TOPIC_ARN = os.environ.get('SNS_TOPIC_ARN')

def lambda_handler(event, context):
    if not SNS_TOPIC_ARN:
        print("Lỗi: Chưa cấu hình biến môi trường SNS_TOPIC_ARN.")
        return

    try:
        backups = backup_client.list_backup_jobs(MaxResults=1)['BackupJobs']
    except Exception as e:
        print(f"Lỗi khi lấy danh sách backup: {e}")
        return

    if not backups:
        print("Không tìm thấy backup nào.")
        return

    last_backup = backups[0]
    print(f"Kiểm tra backup: {last_backup['BackupVaultName']} - {last_backup['State']}")

    if last_backup['State'] != 'COMPLETED':
        sns.publish(
            TopicArn=SNS_TOPIC_ARN,
            Message=f"Backup thất bại: {last_backup}",
            Subject="Backup Verification Failed"
        )
    else:
        print("Backup OK.")
```
![VPC](/images/2.prerequisite/4.2.4.1.png)
------------------------------------------------------------------------

## 3. Lambda Function 2 --- Notification Lambda

### 3.1 Tạo Lambda

1.  Vào **AWS Console** → **Lambda** → **Create function**.
2.  Function name: `BackupNotificationLambda`.
3.  Runtime: `Python 3.10`.
4.  Role: chọn **Create a new role with basic Lambda permissions**.
![VPC](/images/2.prerequisite/4.3.1.png)
### 3.2 Gán quyền SNS Publish

IAM Role → **Add inline policy**:

``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sns:Publish",
            "Resource": "*"
        }
    ]
}
```
![VPC](/images/2.prerequisite/4.3.2.png)
### 3.3 Thêm biến môi trường

-   Key: `SNS_TOPIC_ARN`\
-   Value: ARN của SNS Topic.
![VPC](/images/2.prerequisite/4.3.3.png)
### 3.4 Code Python

``` python
import boto3
import os
import json

sns = boto3.client('sns')
SNS_TOPIC_ARN = os.environ.get('SNS_TOPIC_ARN')

def lambda_handler(event, context):
    print("Nhận event từ AWS Backup:")
    print(json.dumps(event, indent=2, ensure_ascii=False))

    if not SNS_TOPIC_ARN:
        print("Lỗi: Chưa cấu hình SNS_TOPIC_ARN trong Environment variables.")
        return

    try:
        sns.publish(
            TopicArn=SNS_TOPIC_ARN,
            Message=f"Backup/Restore thất bại. Chi tiết: {json.dumps(event, ensure_ascii=False)}",
            Subject="AWS Backup Notification"
        )
        print("Gửi thông báo SNS thành công.")
    except Exception as e:
        print(f"Lỗi khi gửi SNS: {e}")
```
![VPC](/images/2.prerequisite/4.3.4.png)
------------------------------------------------------------------------

## 4. Kết nối Lambda với AWS Backup Events (EventBridge)

### 4.1 Mở EventBridge

-   AWS Console → tìm **EventBridge** → **Rules** → **Create rule**.

![VPC](/images/2.prerequisite/4.4.1.png)
### 4.2 Cấu hình Rule

-   Name: `BackupFailEvents`.
-   Event Source: **AWS events or EventBridge partner events**.
-   Event pattern:

``` json
{
  "source": ["aws.backup"],
  "detail-type": ["Backup Job State Change", "Restore Job State Change"],
  "detail": {
    "state": ["FAILED"]
  }
}
```
![VPC](/images/2.prerequisite/4.4.2.1.png)
-   Target: chọn Lambda `BackupNotificationLambda` hoặc
    `BackupVerificationLambda`.
![VPC](/images/2.prerequisite/4.4.2.2.png)
![VPC](/images/2.prerequisite/4.4.2.3.png)
------------------------------------------------------------------------
