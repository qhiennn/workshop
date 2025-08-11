---
title : "Prepare AWS Environment"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

# 🚀 Guide to Creating an EC2 Instance on AWS

## 1. Log in to AWS Console
- Go to [https://console.aws.amazon.com/](https://console.aws.amazon.com/)  
- Select **EC2** from the service menu.

---

## 2. Create an EC2 Instance
1. **Click** `Launch instance`.  
2. **Name**: e.g., `EC2-DynamoDB-Workshop`.  
3. **Select Amazon Machine Image (AMI)**:  
   - `Amazon Linux 2 AMI (HVM), SSD Volume Type`.  
4. **Choose Instance Type**:  
   - `t2.micro` (Free Tier).  
5. **Create or select a Key Pair**:  
   - If you don’t have one, choose **Create new key pair** and download the `.pem` file.  
6. **Configure Network**:  
   - VPC: Select the default VPC or a custom VPC.  
   - Subnet: Choose a subnet in your preferred region.  
   - Enable Auto-assign Public IP: **Yes**.  
7. **Security Group**:  
   - Create a new one or choose an existing Security Group with rules:  
     - `SSH` (Port 22) - Your IP.  
     - (Optional) Add HTTP (Port 80) and HTTPS (Port 443) depending on your project.  
8. **Storage**:  
   - 8 GiB (SSD GP2) is sufficient for the workshop.  
9. **Click** `Launch Instance`.  

![VPC](/images/2.prerequisite/EC2.png)

---

## 3. Connect to the EC2 Instance
1. Go to **EC2 Dashboard** → Select the instance you just created.  
2. Click **Connect** → Select the **SSH client** tab.  
3. Run the following commands on your local machine:  
```bash
chmod 400 your-key.pem
ssh -i "your-key.pem" ec2-user@<Public-IP>
```

![VPC](/images/2.prerequisite/1.png)
