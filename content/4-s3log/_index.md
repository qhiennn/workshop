---
title : "🚀 Deploy AWS Backup Alert & Verification with Lambda and SNS"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---

## 1. Prepare SNS Topic (send Email/SMS notifications)

### 1.1 Create SNS Topic

1. Open **AWS Console** → search **SNS** → **Topics** → **Create topic**.
2. Select **Standard**.
3. Name: `BackupAlertsTopic`.
4. Click **Create topic**.
![VPC](/images/2.prerequisite/4.1.1.1.png)

### 1.2 Create Subscription (Email/SMS)

1. In the SNS Topic you just created → **Create subscription**.
2. Protocol: `Email` (or `SMS`).
3. Endpoint: enter your email or phone number.
4. Click **Create subscription**.
5. **Confirm** via email (mandatory if choosing Email).
![VPC](/images/2.prerequisite/4.2.2.png)
![VPC](/images/2.prerequisite/4.2.3.png)


------------------------------------------------------------------------

## 2. Lambda Function 1 --- Backup Verification

### 2.1 Create Lambda

1. Go to **AWS Console** → **Lambda** → **Create function**.
2. **Author from scratch**.
3. Function name: `BackupVerificationLambda`.
4. Runtime: `Python 3.10`.
![VPC](/images/2.prerequisite/4.22.1.png)
5. Role: choose **Create a new role with basic Lambda permissions**.
6. Click **Create function**.
![VPC](/images/2.prerequisite/4.22.2.png)

### 2.2 Assign permissions to Lambda

Go to **IAM Console** → select the Lambda's role → **Add inline policy** →
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
Save as `LambdaBackupPolicy`.
![VPC](/images/2.prerequisite/4.22.333.png)

### 2.3 Add environment variable

- Key: `SNS_TOPIC_ARN`  
- Value: ARN of the SNS Topic you just created.
![VPC](/images/2.prerequisite/4.222.3.png)

### 2.4 Python Code
- Deploy code:
``` python
import boto3
import os

backup_client = boto3.client('backup')
sns = boto3.client('sns')

SNS_TOPIC_ARN = os.environ.get('SNS_TOPIC_ARN')

def lambda_handler(event, context):
    if not SNS_TOPIC_ARN:
        print("Error: SNS_TOPIC_ARN environment variable not configured.")
        return

    try:
        backups = backup_client.list_backup_jobs(MaxResults=1)['BackupJobs']
    except Exception as e:
        print(f"Error fetching backup list: {e}")
        return

    if not backups:
        print("No backups found.")
        return

    last_backup = backups[0]
    print(f"Checking backup: {last_backup['BackupVaultName']} - {last_backup['State']}")

    if last_backup['State'] != 'COMPLETED':
        sns.publish(
            TopicArn=SNS_TOPIC_ARN,
            Message=f"Backup failed: {last_backup}",
            Subject="Backup Verification Failed"
        )
    else:
        print("Backup OK.")
```
![VPC](/images/2.prerequisite/4.2.4.1.png)
------------------------------------------------------------------------

## 3. Lambda Function 2 --- Notification Lambda

### 3.1 Create Lambda

1. Go to **AWS Console** → **Lambda** → **Create function**.
2. Function name: `BackupNotificationLambda`.
3. Runtime: `Python 3.10`.
4. Role: choose **Create a new role with basic Lambda permissions**.
![VPC](/images/2.prerequisite/4.3.1.png)

### 3.2 Assign SNS Publish permissions

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

### 3.3 Add environment variable

- Key: `SNS_TOPIC_ARN`  
- Value: ARN of the SNS Topic.
![VPC](/images/2.prerequisite/4.3.3.png)

### 3.4 Python Code

``` python
import boto3
import os
import json

sns = boto3.client('sns')
SNS_TOPIC_ARN = os.environ.get('SNS_TOPIC_ARN')

def lambda_handler(event, context):
    print("Received event from AWS Backup:")
    print(json.dumps(event, indent=2, ensure_ascii=False))

    if not SNS_TOPIC_ARN:
        print("Error: SNS_TOPIC_ARN not configured in Environment variables.")
        return

    try:
        sns.publish(
            TopicArn=SNS_TOPIC_ARN,
            Message=f"Backup/Restore failed. Details: {json.dumps(event, ensure_ascii=False)}",
            Subject="AWS Backup Notification"
        )
        print("SNS notification sent successfully.")
    except Exception as e:
        print(f"Error sending SNS: {e}")
```
![VPC](/images/2.prerequisite/4.3.4.png)
------------------------------------------------------------------------

## 4. Connect Lambda with AWS Backup Events (EventBridge)

### 4.1 Open EventBridge

- AWS Console → search **EventBridge** → **Rules** → **Create rule**.

![VPC](/images/2.prerequisite/4.4.1.png)

### 4.2 Configure Rule

- Name: `BackupFailEvents`.
- Event Source: **AWS events or EventBridge partner events**.
- Event pattern:

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

- Target: select Lambda `BackupNotificationLambda` or
  `BackupVerificationLambda`.
![VPC](/images/2.prerequisite/4.4.2.2.png)
![VPC](/images/2.prerequisite/4.4.2.3.png)
------------------------------------------------------------------------
