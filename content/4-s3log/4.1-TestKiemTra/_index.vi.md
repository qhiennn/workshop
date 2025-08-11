---
title : "Test Kiểm Tra"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

## 1. Test **BackupNotificationLambda** bằng EventBridge test event

1. **Đi tới Lambda function**: `BackupNotificationLambda`
2. Chọn **Test** → **Configure test event**
3. Chọn **Create new test event**
4. **Name**: `testBackupFail`
5. Dán EventBridge-style JSON sau vào **Event JSON**:
![VPC](/images/2.prerequisite/4.5.3.png)
![VPC](/images/2.prerequisite/4.5.2.png)
```json
{
  "version": "0",
  "id": "test-id-123",
  "detail-type": "Backup Job State Change",
  "source": "aws.backup",
  "account": "123456789012",
  "time": "2025-08-10T12:00:00Z",
  "region": "ap-southeast-1",
  "detail": {
    "state": "FAILED",
    "backupJobId": "test-backup-123",
    "backupVaultName": "Default",
    "resourceArn": "arn:aws:rds:ap-southeast-1:123456789012:db:mydb"
  }
}
```

1. **Save** → **Test** → Quan sát kết quả và kiểm tra email.
![VPC](/images/2.prerequisite/4.5.1.png)
---

## 2. Test **BackupVerificationLambda** (run manual)

1. **Đi tới Lambda function**: `BackupVerificationLambda`
2. Chọn **Test**
3. Tạo test event `{}` → **Test**
4. Kiểm tra logs:
   - Nếu Lambda publish SNS → Bạn sẽ nhận email.
   - Nếu không có backup jobs → Sẽ thấy log `"No backup jobs found."`
![VPC](/images/2.prerequisite/4.5.1.2.png)
![VPC](/images/2.prerequisite/4.5.1.1.png)
![VPC](/images/2.prerequisite/4.5.1.5.png)
---

## 3. Kiểm tra CloudWatch Logs

1. Vào **AWS Console** → **CloudWatch** → **Logs**
![VPC](/images/2.prerequisite/4.6.1.png)
1. Chọn nhóm log:
   - `/aws/lambda/BackupNotificationLambda`
![VPC](/images/2.prerequisite/4.6.2.png)
   - `/aws/lambda/BackupVerificationLambda`
![VPC](/images/2.prerequisite/4.6.3.png)
1. Mở **stream log mới nhất**
2. Đọc log để xác nhận kết quả.
- BackupNotificationLambda
![VPC](/images/2.prerequisite/4.6.4.png)
- BackupVerificationLambda
![VPC](/images/2.prerequisite/4.6.5.png)