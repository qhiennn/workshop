+++
title = "Resource Cleanup"
date = 2021
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

After completing the lab, you need to delete the AWS resources created to avoid incurring unnecessary costs. This section will guide you step-by-step on how to delete the resources created during the **Backup & Disaster Recovery** workshop.

#### Delete EC2 Instance

1. Go to the [EC2 management console](https://console.aws.amazon.com/ec2/v2/home)  
   + Click **Instances**.  
   + Select **EC2BackUp**.  
   + Click **Instance state**.  
   + Click **Terminate instance**, then click **Terminate** to confirm.  

![VPC](/images/2.prerequisite/5.1.png)

#### Delete Backup Plan & Backup Vault
1. Go to **AWS Console** → **AWS Backup**.  
2. **Delete Backup Plan**:  
   - Select **Backup plans**.  
   - Select the plan created in the lab.  
   - Click **Delete** → Confirm.  
3. **Delete Backup Vault** (if no longer needed):  
   - Go to **Backup vaults**.  
   - Select your vault (e.g., `Default` or custom name).  
   - Delete all Recovery Points inside the vault.  
   - Click **Delete vault**.  

![VPC](/images/2.prerequisite/5.2.png)  
![VPC](/images/2.prerequisite/5.2.1.png)  
![VPC](/images/2.prerequisite/5.2.2.png)  

### Delete DynamoDB Tables
1. Go to **AWS Console** → **DynamoDB** → **Tables**.  
2. Select and delete each table created:  
   - `CategoryHoiThao`  
   - `HistoryBook`  
   - `HistoryBookUsers`  
   - `HoiThao`  
   - `Role`  
   - `User`  
   - `UserGiaiDau`  
   - `UserHoiThao`  
   - `UserLiveShow`  
   - `UserRole`  
3. Click **Delete table** → Confirm.  

![VPC](/images/2.prerequisite/5.3.1.png)  
![VPC](/images/2.prerequisite/5.3.png)  

#### Delete DocumentDB Cluster
1. Go to **AWS Console** → **Amazon DocumentDB**.  
2. Select the cluster.  
3. Click **Actions** → **Delete cluster**.  
4. Tick the option to **delete snapshots** (if you don’t need to keep them) → Confirm.  

![VPC](/images/2.prerequisite/5.4.png)  

### Delete Lambda Functions
1. Go to **AWS Console** → **Lambda**.  
2. Delete the functions:  
   - `BackupNotificationLambda`  
   - `BackupVerificationLambda`  

![VPC](/images/2.prerequisite/5.5.png)  

### Delete SNS Topics & Subscriptions
1. Go to **AWS Console** → **Amazon SNS**.  
2. Delete the topic used for notifications.  
3. Delete the related subscriptions.  

![VPC](/images/2.prerequisite/5.6.png)  
![VPC](/images/2.prerequisite/5.6.1.png)  

### Delete IAM Roles and Policies
1. Go to **AWS Console** → **IAM**.  
2. Delete the roles created for Lambda and database access.  
3. Delete the custom policies created in the lab.  

![VPC](/images/2.prerequisite/5.7.png)  
