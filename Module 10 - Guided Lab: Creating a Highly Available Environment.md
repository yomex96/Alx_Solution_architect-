
# Guided Lab: Creating a Highly Available Environment

## Lab Overview and Objectives

Critical business systems should be deployed as **highly available applications**—applications remain operational even when some components fail.

To achieve high availability in AWS, you run services across **multiple Availability Zones (AZs)**.

* Many AWS services are inherently highly available (e.g., **Elastic Load Balancers**).
* Others can be configured for high availability (e.g., running **EC2 instances in multiple AZs**).

In this lab, you start with an application that runs on a single EC2 instance and then make it **highly available**.

✅ After completing this lab, you will be able to:

* Inspect a provided **VPC**
* Create an **Application Load Balancer (ALB)**
* Create an **Auto Scaling group (ASG)**
* Test the application for **high availability**

📌 At the end of this lab, your architecture will look like this:
**Highly Available Architecture with VPC, ALB, Auto Scaling, and RDS.**

---

## Duration

⏳ Approximately **40 minutes**

---

## AWS Service Restrictions

⚠️ In this lab environment, only specific AWS services and actions are available. Attempting to use unsupported services may result in errors.

---

## Accessing the AWS Management Console

1. At the top of these instructions, choose **Start Lab**.
2. Wait until the green circle appears next to the AWS link.
3. Choose the **AWS link** to open the Management Console in a new browser tab.
4. Arrange your screen to view both the **Console** and **Lab Instructions** side-by-side.

📌 Do **not** change the AWS Region unless instructed.

---

## Task 1: Inspecting Your VPC

The lab environment is preconfigured with the following resources:

* **VPC**
* Public & private subnets in **two AZs**
* **Internet Gateway (IGW)** for public subnets
* **NAT Gateway** in one public subnet
* **Amazon RDS** instance in a private subnet

### Steps:

1. Open the **VPC console**.
2. From **Filter by VPC**, select **Lab VPC**.
3. Review:

   * **CIDR block**: `10.0.0.0/16`
   * **Public Subnet 1**: `10.0.0.0/24`
   * **Route Table**: Routes local traffic within VPC and internet-bound traffic to IGW.
   * **Network ACL**: Default allows all traffic.
   * **Internet Gateway**: Already attached.
   * **Security Groups**:

     * `Inventory-DB` allows MySQL (3306) from VPC (`10.0.0.0/16`).

---

## Task 2: Creating an Application Load Balancer (ALB)

You’ll deploy an **ALB** to distribute traffic across multiple application servers.

### Steps:

1. Open the **EC2 console → Load Balancers → Create Load Balancer**.
2. Select **Application Load Balancer**.
3. Configure:

   * **Name**: `Inventory-LB`
   * **VPC**: `Lab VPC`
   * **Subnets**: Public Subnet 1 & Public Subnet 2
   * **Security Group**: Create new → `Inventory-LB` (allow **HTTP & HTTPS** from Anywhere).
4. Create a **Target Group**:

   * **Name**: `Inventory-App`
   * **Target type**: Instances
   * **VPC**: `Lab VPC`
   * **Health checks**: Every **10s**, Healthy threshold = **2**
5. Attach target group to ALB.

---

## Task 3: Creating an Auto Scaling Group (ASG)

### 3.1 Create an AMI

1. From EC2 console, select **Web Server 1**.
2. **Actions → Image and templates → Create Image**.

   * **Name**: `Web Server AMI`
   * **Description**: `Lab AMI for Web Server`

---

### 3.2 Create a Launch Template

1. Name: `Inventory-LT`
2. **AMI**: `Web Server AMI`
3. **Instance type**: `t2.micro`
4. **Key pair**: `vockey`
5. **Security group**: `Inventory-App`
6. **IAM Role**: `Inventory-App-Role`
7. **User Data Script**:

   ```bash
   #!/bin/bash
   yum install -y httpd mysql
   amazon-linux-extras install -y php7.2
   wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/12-lab-mod10-guided-Scaling/s3/scripts/inventory-app.zip
   unzip inventory-app.zip -d /var/www/html/
   wget https://github.com/aws/aws-sdk-php/releases/download/3.62.3/aws.zip
   unzip aws -d /var/www/html
   chkconfig httpd on
   service httpd start
   ```

---

### 3.3 Create Auto Scaling Group

1. **Name**: `Inventory-ASG`
2. **VPC**: `Lab VPC`
3. **Subnets**: Private Subnet 1 & 2
4. **Load Balancer**: Attach to `Inventory-App` target group
5. **Capacity**:

   * Min = 2
   * Desired = 2
   * Max = 2
6. **Tags**:

   * Key: `Name`, Value: `Inventory-App`

---

## Task 4: Updating Security Groups

### 4.1 Load Balancer SG

* Already allows **HTTP/HTTPS from Anywhere**.

### 4.2 Application SG (`Inventory-App`)

* Add inbound rule:

  * **HTTP (80)** from **Inventory-LB SG**

### 4.3 Database SG (`Inventory-DB`)

* Modify inbound rule:

  * Allow **MySQL/Aurora (3306)** only from **Inventory-App SG**

---

## Task 5: Testing the Application

1. Open **Target Groups → Inventory-App → Targets**.

   * Ensure **2 healthy instances**.
2. Copy the **ALB DNS name**.
3. Open in browser → should show app running.
4. Refresh page → requests should toggle between instances.

---

## Task 6: Testing High Availability

1. In **EC2 console**, terminate one `Inventory-App` instance.
2. Reload the app in browser → app remains available.
3. ASG will automatically launch a replacement instance.

---

## Optional Task 1: Making the Database Highly Available

1. Open **RDS console → inventory-db → Modify**.
2. Enable **Multi-AZ Deployment → Create standby instance**.
3. Scale up:

   * **Instance class**: `db.t3.small`
   * **Storage**: `20 GB`
4. Apply immediately.

---

✅ **End of Lab: You now have a fully HA application running in AWS!**

