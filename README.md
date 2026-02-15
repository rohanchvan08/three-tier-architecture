# Three Tier Architecture 
## 📌 Description
In this project, a secure and scalable three-tier architecture was deployed on AWS using VPC, EC2 instances, and RDS.java is used for backend and tomcat. The architecture separates the application into three independent layers: Web, Application, and Database.

This design improves security, availability, performance, and maintainability.

## 🔷 What is Three-Tier Architecture?

**Three-tier architecture separates an application into three layers:**

- Presentation Layer (Proxy-server) → Handles user requests (Nginx on EC2)

- Application Layer (App-server) → Business logic (Java/tomcat on EC2)

- Database Layer (DB-server) → Data storage (RDS)

## 🏗️ Architecture:

![Reference Image](/img/Architecture.png) 


## :bulb: Deployment Steps:
## Step 1: Create the Virtual Private Cloud (VPC)
- VPC Name - Three-tier-VPC
- CIDR Range - 10.0.0.0/16
- Create VPC
  
![Reference Image](/img/img1.png) 

## Step 2: Create Three Subnet
### Public Subnet 1
- Select VPC - Three-tier-VPC
- Subnet Name - Public-Subnet-1
- Avalibility Zone
- IPv4 Subnet CIDR Block - 10.0.0.0/20
- Add new subnet
  
### Private Subnet 1
- Select VPC - Three-tier-VPC
- Subnet Name - Private-Subnet-1
- Avalibility Zone
- IPv4 Subnet CIDR Block - 10.0.16.0/20
- Add new subnet

### Private Subnet 2
- Select VPC - Three-tier-VPC
- Subnet Name - Private-Subnet-2
- Avalibility Zone
- IPv4 Subnet CIDR Block - 10.0.32.0/20
- Create Subnet

![Reference Image](/img/img2.png) 

## Step 3: Create internet gateways (IGW)
- Internet Gateway Name - Three-tier-IGW
- Select VPC - Three-tier-VPC
- Create internet gateway
 
![Reference Image](/img/img3.png)

## Step 4: Create Network Address Translation (NAT).
#### It is a method that allows private instances in a VPC to access the internet while keeping their private IP addresses hidden.
- NAT Gateway Name - Three-tier-NAT
- Select VPC - Three-tier-VPC
- Create NAT
 
![Reference Image](/img/img4.png)

## Step 5: Create Pubic & Private Route Table.
#### **Public-RT**
- Route table name - Public-RT
- Select VPC - Three-tier-VPC
- Create Route Table
- Edit assign Public IP
- Edit routes
- Add route - Internet Gateway
- Save and changes  
- Subnet Associations - Public-subnet-1
- Save Association
  
#### **Private-RT**
- Route table name - Private-RT
- Select VPC - Three-tier-VPC
- Create Route Table
- Edit routes
- Add route - NAT gateway
- Save and changes  
- Subnet Associations - Private-subnet-1 & Private-subnet-2
- Save Association
  
![Reference Image](/img/img5.png)

## Step 6: Create Security Group.
- Security Group Name - Three-tier-SG
- Description
- Select VPC
- Add inbound rules
  - SSH - 22
  - HTTP - 80
  - MYSQL/aurora- 3306
  - Tomcat - 8080
- Create Security Group

![Reference Image](/img/img6.png)

## Step 7: Launch Three Instance.
### **Proxy-server**
- Instance name - Proxy-server
- Select AMI - Amazon Linux
- Instance Type - t3.small
- Key pair - First-cal-key.pem
- Select VPC - Three-tier-VPC
- Select Subnet - Public-subnet-1
- Select Availability Zone
- Auto-assign Public IP - Enable
- Select Existing Security Group - Three-tier-SG 
- Launch instance
  
### **App-server**
- Instance name - App-server
- Select AMI - Amazon Linux
- Instance Type - t3.small
- Key pair - First-cal-key.pem
- Select VPC - Three-tier-VPC
- Select Subnet - Private-subnet-1
- Select Availability Zone
- Auto-assign Public IP - Disable
- Select Existing Security Group - Three-tier-SG 
- Launch instance
  
### **DB-server**
- Instance name - DB-server
- Select AMI - Amazon Linux
- Instance Type - t3.small
- Key pair - First-cal-key.pem
- Select VPC - Three-tier-VPC
- Select Subnet - Private-subnet-2
- Select Availability Zone
- Auto-assign Public IP - Disable
- Select Existing Security Group - Three-tier-SG 
- Launch instance
  
![Reference Image](/img/img7.png)

## Step 8: Access The Proxy-server & Copy the Key-pair in home directory.
- ssh -i "first-cal-key. pem" ec2-user@3.85.211.168
- exit
- scp -i first-cal-key.pem first-cal-key.pem ec2-user@3.85.211.168:/home/ec2-user/

![Reference Image](/img/img8.png)

## Step 9: Install Nginx Webserver & Add the proxy_pass.
```bash
sudo yum update
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```
```bash
cd /etc/nginx/nginx.conf
 # Add content end of the file.
 location / {
 proxy_pass http://(App-server-private-IP):8080/student/;
 }

 Restart nginx
 ```

![Reference Image](/img/img9.png)

## Step 10: Install java in app-server
![Reference Image](/img/img10.png)


## Step 11: Install Tomcat and unzip the tomcat file.
![Reference Image](/img/img11.png)

## Step 12: Download student regestration directory in app-server.

![Reference Image](/img/img12.png)

## Step 13: Create the RDS
- Create database 
- Full configuration
- MariaDB
- Database name - Three-tier-RDS
- Auto generated password
- Select VPC - Three-tier-VPC
- Create database
  
![Reference Image](/img/img13.png)

## Step 14: Install mariadb105-server in DB-server

![Reference Image](/img/img14.png)


## Step 15: Create database and table.

![Reference Image](/img/img15.png)

## Step 16: Copy IP address 


![Reference Image](/img/img16.png)

## Step 17: Paste the IP address in broswer and insert student data.

![Reference Image](/img/img17.png)

## Step 18: Display students data in broswer.

![Reference Image](/img/img18.png)

## Step 19: Display student data in RDS.

![Reference Image](/img/img19.png)


## :pushpin:**Summary**

I deployed a three-tier architecture in AWS by creating a custom VPC with public and private subnets. The web server was placed in the public subnet, the application server in a private subnet, and the database was configured using RDS in a private subnet. Security groups were configured to allow restricted communication between tiers, ensuring secure and scalable architecture