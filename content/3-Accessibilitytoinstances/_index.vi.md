---
title : "Tạo kết nối đến máy chủ EC2"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---
## 1. Tạo kết nối đến máy chủ EC2
- Tài khoản AWS có quyền:
- `AWSBackupFullAccess`
- `AmazonDynamoDBFullAccess`
- Quyền sử dụng **AWS KMS** nếu muốn mã hóa backup.
- Đã đăng nhập vào **AWS Management Console**.

---

## 2. Truy cập dịch vụ AWS Backup
1. Mở AWS Console, tìm **AWS Backup** trên thanh tìm kiếm.
2. Nhấn chọn để vào giao diện dịch vụ.
![VPC](/images/2.prerequisite/3.2.png)
---

## 3. Tạo Backup Vault (Kho lưu trữ backup)
1. Vào **Backup vaults** → **Create backup vault**.
2. Nhập:
 - **Backup vault name**: `DynamoDBBackupVault`
 - **Encryption key**: chọn **AWS managed key (Default)** hoặc **KMS key**.
3. Nhấn **Create backup vault**.
![VPC](/images/2.prerequisite/3.3.png)
---

## 4. Tạo Backup Plan (Kế hoạch sao lưu)
1. Vào **Backup plans** → **Create backup plan**.
2. Chọn **Build a new plan**.
3. Điền:
 - **Backup plan name**: `DynamoDBDailyBackupPlan`
4. Trong **Backup rule**:
 - **Rule name**: `DailyBackup`
 - **Backup vault**: chọn `DynamoDBBackupVault`
 - **Frequency**: `Daily`
 - **Backup window**: mặc định hoặc chọn giờ sao lưu (VD: 02:00 UTC)
 - **Lifecycle**: Expire after 30 days
5. Nhấn **Create plan**.
![VPC](/images/2.prerequisite/3.4.png)
---

## 5. Gán DynamoDB vào Backup Plan
1. Trong giao diện kế hoạch, nhấn **Assign resources**.
2. **Resource type**: `DynamoDB`
3. **Assign by**: `Resource ID`
4. Tick chọn tất cả các bảng.
5. **IAM role**: `AWSBackupDefaultServiceRole`
 - Nếu chưa có role, tạo trong IAM:
   - **Service**: Backup
   - Gán quyền:
     - `AWSBackupServiceRolePolicyForBackup`
     - `AWSBackupServiceRolePolicyForRestores`
6. Nhấn **Assign resources**.
![VPC](/images/2.prerequisite/3.5.1.png)
![VPC](/images/2.prerequisite/3.5.2.png)
---

## 6. Kiểm tra cấu hình Backup
- Vào **Protected resources** để kiểm tra các bảng đã gán.
![VPC](/images/2.prerequisite/3.6.1.png)
- Vào **Backup plans → DynamoDBDailyBackupPlan** để xem lịch backup.
![VPC](/images/2.prerequisite/3.6.2.png)

---

## 7. Chạy thử sao lưu thủ công
1. Vào **Protected resources** → chọn bảng, ví dụ `CategoryHoiThao`.
2. Nhấn **Create on-demand backup**.
3. Nhập:
 - **Backup name**: `TestBackup_CategoryHoiThao`
 - **Backup vault**: `DynamoDBBackupVault`
4. Nhấn **Create on-demand backup**.
5. Xem kết quả tại **Backup vaults → DynamoDBBackupVault**.
![VPC](/images/2.prerequisite/3.7.png)

---

## 8. Khôi phục dữ liệu từ backup (Restore Test)
1. Vào **Backup vaults → DynamoDBBackupVault**.
2. Chọn bản backup → **Restore**.
3. Nhập:
 - **Target table name**: `CategoryHoiThao_RestoreTest`
4. Nhấn **Restore backup**.
5. Kiểm tra tại **DynamoDB → Tables**.
![VPC](/images/2.prerequisite/3.8.png)

---

## 9. Lưu ý quan trọng
- **Chi phí**: Tính theo dung lượng và thời gian lưu trữ.
- **Bảo mật**: Nên bật **KMS Encryption**.
- **Giám sát**: Có thể bật cảnh báo qua **CloudWatch**.
