# Project: Highly Available PHP Web Application on AWS

## 📌 Overview and Objectives
Throughout the **AWS Academy Cloud Architecting (ACA) course**, you completed hands-on labs with AWS services. This final project challenges you to apply those skills in a real-world architecture scenario.

You will design and deploy a **highly available, secure PHP web application** backed by a **MySQL database**, while following AWS best practices.

By the end of this project, you will be able to:

- ✅ Create an **Amazon RDS MySQL database** and import data from a SQL dump file  
- ✅ Deploy a **highly available PHP application** across multiple Availability Zones  
- ✅ Secure application servers and backend systems (private access only)  
- ✅ Configure an **Application Load Balancer** to distribute traffic  
- ✅ Enable **automatic scaling** using an EC2 Auto Scaling Group and Launch Template  
- ✅ Use **AWS Secrets Manager** for credential storage  
- ✅ Prevent direct public access to the database and application servers  

---

## ⚠️ Lab Environment & Budget Considerations
- This lab environment is **long-lived**: resources persist across sessions.  
- AWS Budgets updates every **8–12 hours**, so the displayed budget may lag.  
- Overspending will disable your account — resources and progress will be lost.  

### Tips to Control Costs:
- Launch only the **instances you need**.  
- **Stop or delete** EC2, RDS, NAT Gateways, or Cloud9 when not in use.  
- Use the [AWS Pricing Calculator](https://calculator.aws/) for cost estimates.  

---

## 📖 Scenario
You are working with **Example Social Research Organization**, a nonprofit that provides a PHP website for researchers to access **global development statistics** (e.g., life expectancy per country over the last 10 years).

- The current setup runs **PHP + MySQL** on a single EC2 instance in a public subnet.  
- This design is **not scalable** and **not secure**.  
- Your job: **migrate the site to a secure, highly available AWS architecture**.  

---

## 🏗️ Solution Requirements
- **Database**: Host MySQL in Amazon RDS (secure, private access).  
- **Application**: Deploy PHP across multiple EC2 instances in **private subnets**.  
- **Access**: Only the ALB is public-facing.  
- **Load balancing**: Use an **Application Load Balancer** for traffic distribution.  
- **Scaling**: Configure Auto Scaling Group with Launch Template.  
- **Secrets**: Store DB credentials in **AWS Secrets Manager**.  
- **Data Migration**: Import SQL dump into RDS instance.  
- **Testing**: Validate functionality via ALB DNS name.  

---

## 🖥️ Architecture
### Starting State:
- Single EC2 instance (PHP + MySQL) in public subnet.  

### Final State:
- **RDS MySQL DB** (private subnet, credentials in Secrets Manager)  
- **PHP web servers** in Auto Scaling Group (private subnets)  
- **Application Load Balancer** (public subnets) → routes traffic to PHP servers  
- **Secure VPC architecture** with multiple Availability Zones  

---

## 🔨 Steps to Implement

### Step 1: Create RDS MySQL Database
- Engine: **MySQL**  
- Database name: `countries`  
- Security group: Allow access **only from PHP app servers**  
- Store DB credentials in **Secrets Manager**  

**Reference:**  
[AWS Academy Lab – Creating an Amazon RDS Database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html)

---

### Step 2: Create Application Load Balancer
- Deploy ALB in **2+ Availability Zones** (public subnets).  
- Listener: Port 80 (HTTP).  
- Target group: PHP EC2 instances.  

**Reference:**  
[Getting Started with Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancers.html)

---

### Step 3: Create Application Servers in Auto Scaling Group
- Create a **Launch Template** (Amazon Linux 2023, PHP preinstalled).  
- Use **Example-LT** provided in lab (already contains SQL dump + PHP app).  
- Configure Auto Scaling Group across private subnets.  
- Attach ALB Target Group to Auto Scaling Group.  

**Tip:** Start with default size, use **Target Tracking policy (CPU utilization)**.  

---

### Step 4: Import Data into RDS MySQL
- Use **SQL dump file** available in launch template.  
- Retrieve DB credentials from Secrets Manager.  
- Import data into `countries` database.  

**Reference:**  
[Importing Data to RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Procedural.Importing.html)

---

### Step 5: Test Application & Validate Data
- Open **ALB DNS name** in browser.  
- Run sample queries on the PHP website.  
- Validate results are fetched from **RDS MySQL database**.  

---

## 📝 Deliverables
1. **Architecture Diagram** of final solution  
2. **Deployed PHP application** running behind ALB  
3. **Written Summary** of your design decisions:
   - Why RDS instead of EC2 MySQL  
   - Why ALB + Auto Scaling Group for HA  
   - Why Secrets Manager instead of hardcoded credentials  
   - Security best practices (private subnets, no public DB access)  

---

## ✅ Submission
- At top of instructions → **Submit** → confirm **Yes**  
- Wait for **Grades panel** to update  
- Re-submit as needed (last submission is final)  

---

## 🛑 Ending Your Session
- Choose **End Lab → Yes**  
- Resources remain in your AWS account for next session.  
- To avoid budget drain: stop EC2, RDS, NAT Gateway.  

---
