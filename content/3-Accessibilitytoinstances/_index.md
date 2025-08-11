---
title : "Create Connection to EC2 Instance"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

## 1. Create a connection to the EC2 instance
- AWS account with permissions:  
  - `AWSBackupFullAccess`  
  - `AmazonDynamoDBFullAccess`  
  - AWS KMS permissions if you want to enable backup encryption.  
- Logged in to **AWS Management Console**.

---

## 2. Access AWS Backup service
1. Open AWS Console, search for **AWS Backup** in the search bar.  
2. Click to open the service interface.  
![VPC](/images/2.prerequisite/3.2.png)

---

## 3. Create a Backup Vault
1. Go to **Backup vaults** → **Create backup vault**.  
2. Enter:  
   - **Backup vault name**: `DynamoDBBackupVault`  
   - **Encryption key**: Select **AWS managed key (Default)** or **KMS key**.  
3. Click **Create backup vault**.  
![VPC](/images/2.prerequisite/3.3.png)

---

## 4. Create a Backup Plan
1. Go to **Backup plans** → **Create backup plan**.  
2. Select **Build a new plan**.  
3. Fill in:  
   - **Backup plan name**: `DynamoDBDailyBackupPlan`  
4. Under **Backup rule**:  
   - **Rule name**: `DailyBackup`  
   - **Backup vault**: select `DynamoDBBackupVault`  
   - **Frequency**: `Daily`  
   - **Backup window**: default or set your preferred backup time (e.g., 02:00 UTC)  
   - **Lifecycle**: Expire after 30 days  
5. Click **Create plan**.  
![VPC](/images/2.prerequisite/3.4.png)

---

## 5. Assign DynamoDB tables to the Backup Plan
1. In the backup plan interface, click **Assign resources**.  
2. **Resource type**: `DynamoDB`  
3. **Assign by**: `Resource ID`  
4. Select all tables.  
5. **IAM role**: `AWSBackupDefaultServiceRole`  
   - If the role doesn’t exist, create it in IAM:  
     - **Service**: Backup  
     - Attach permissions:  
       - `AWSBackupServiceRolePolicyForBackup`  
       - `AWSBackupServiceRolePolicyForRestores`  
6. Click **Assign resources**.  
![VPC](/images/2.prerequisite/3.5.1.png)  
![VPC](/images/2.prerequisite/3.5.2.png)

---

## 6. Verify Backup configuration
- Go to **Protected resources** to check assigned tables.  
![VPC](/images/2.prerequisite/3.6.1.png)  
- Go to **Backup plans → DynamoDBDailyBackupPlan** to view backup schedule.  
![VPC](/images/2.prerequisite/3.6.2.png)

---

## 7. Perform a manual backup test
1. Go to **Protected resources** → choose a table, e.g., `CategoryHoiThao`.  
2. Click **Create on-demand backup**.  
3. Enter:  
   - **Backup name**: `TestBackup_CategoryHoiThao`  
   - **Backup vault**: `DynamoDBBackupVault`  
4. Click **Create on-demand backup**.  
5. View results at **Backup vaults → DynamoDBBackupVault**.  
![VPC](/images/2.prerequisite/3.7.png)

---

## 8. Restore data from backup (Restore Test)
1. Go to **Backup vaults → DynamoDBBackupVault**.  
2. Select the backup → **Restore**.  
3. Enter:  
   - **Target table name**: `CategoryHoiThao_RestoreTest`  
4. Click **Restore backup**.  
5. Verify in **DynamoDB → Tables**.  
![VPC](/images/2.prerequisite/3.8.png)

---

## 9. Important notes
- **Cost**: Charged based on storage size and retention time.  
- **Security**: Recommended to enable **KMS Encryption**.  
- **Monitoring**: You can set alerts via **CloudWatch**.
