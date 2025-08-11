+++
title = "Dọn dẹp tài nguyên  "
date = 2021
weight = 5
chapter = false
pre = "<b>5. </b>"
+++


Sau khi hoàn thành bài lab, bạn cần xóa các tài nguyên AWS đã tạo để tránh phát sinh chi phí không cần thiết. Phần này sẽ hướng dẫn từng bước để xóa các tài nguyên đã tạo trong workshop **Backup & Disaster Recovery**.

#### Xóa EC2 instance

1. Truy cập [giao diện quản trị dịch vụ EC2](https://console.aws.amazon.com/ec2/v2/home)
  + Click **Instances**.
  + Click chọn **EC2BackUp**. 
  + Click **Instance state**.
  + Click **Terminate instance**, sau đó click **Terminate** để xác nhận.
  
![VPC](/images/2.prerequisite/5.1.png)

#### Xóa Backup Plan & Backup Vault
1. Vào **AWS Console** → **AWS Backup**.
2. **Xóa Backup Plan**:
   - Chọn **Backup plans**.
   - Chọn kế hoạch đã tạo trong lab.
   - Nhấn **Delete** → Xác nhận.
3. **Xóa Backup Vault** (nếu không còn sử dụng):
   - Vào **Backup vaults**.
   - Chọn Vault của bạn (ví dụ `Default` hoặc tên tùy chỉnh).
   - Xóa toàn bộ Recovery Points bên trong Vault.
   - Nhấn **Delete vault**.

![VPC](/images/2.prerequisite/5.2.png)
![VPC](/images/2.prerequisite/5.2.1.png)
![VPC](/images/2.prerequisite/5.2.2.png)

### Xóa các bảng DynamoDB
1. Vào **AWS Console** → **DynamoDB** → **Tables**.
2. Chọn và xóa từng bảng đã tạo:
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
3. Nhấn **Delete table** → Xác nhận.
  
![VPC](/images/2.prerequisite/5.3.1.png)
![VPC](/images/2.prerequisite/5.3.png)

#### Xóa DocumentDB Cluster
1. Vào **AWS Console** → **Amazon DocumentDB**.
2. Chọn cluster.
3. Chọn **Actions** → **Delete cluster**.
4. Tick chọn **xóa snapshots** (nếu không cần giữ) → Xác nhận.
![VPC](/images/2.prerequisite/5.4.png)
### Xóa Lambda Functions
1. Vào **AWS Console** → **Lambda**.
2. Xóa các hàm:
   - `BackupNotificationLambda`
   - `BackupVerificationLambda`
![VPC](/images/2.prerequisite/5.5.png)
### Xóa SNS Topics & Subscriptions
1. Vào **AWS Console** → **Amazon SNS**.
2. Xóa Topic dùng để gửi thông báo.
3. Xóa các Subscription liên quan.
![VPC](/images/2.prerequisite/5.6.png)
![VPC](/images/2.prerequisite/5.6.1.png)
### Xóa IAM Roles và Policies
1. Vào **AWS Console** → **IAM**.
2. Xóa các Role tạo cho Lambda và truy cập Database.
3. Xóa các Policy tùy chỉnh đã tạo trong lab.
![VPC](/images/2.prerequisite/5.7.png)
