---
title : "Test Verification"
date :  "r Sys.Date()" 
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

## 1. Test **BackupNotificationLambda** using EventBridge test event

1. **Go to Lambda function**: BackupNotificationLambda
2. Select **Test** → **Configure test event**
3. Choose **Create new test event**
4. **Name**: testBackupFail
5. Paste the following EventBridge-style JSON into **Event JSON**:
![VPC](/images/2.prerequisite/4.5.3.png)
![VPC](/images/2.prerequisite/4.5.2.png)
json
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

1. **Save** → **Test** → Observe the result and check your email.
![VPC](/images/2.prerequisite/4.5.1.png)
---

## 2. Test **BackupVerificationLambda** (manual run)

1. **Go to Lambda function**: BackupVerificationLambda
2. Select **Test**
3. Create test event {} → **Test**
4. Check logs:
   - If Lambda publishes SNS → You will receive an email.
   - If there are no backup jobs → You will see the log "No backup jobs found."
![VPC](/images/2.prerequisite/4.5.1.2.png)
![VPC](/images/2.prerequisite/4.5.1.1.png)
![VPC](/images/2.prerequisite/4.5.1.5.png)
---

## 3. Check CloudWatch Logs

1. Go to **AWS Console** → **CloudWatch** → **Logs**
![VPC](/images/2.prerequisite/4.6.1.png)
1. Select log group:
   - /aws/lambda/BackupNotificationLambda
![VPC](/images/2.prerequisite/4.6.2.png)
   - /aws/lambda/BackupVerificationLambda
![VPC](/images/2.prerequisite/4.6.3.png)
1. Open the **latest log stream**
2. Read the log to verify the results.
- BackupNotificationLambda
![VPC](/images/2.prerequisite/4.6.4.png)
- BackupVerificationLambda
![VPC](/images/2.prerequisite/4.6.5.png)
