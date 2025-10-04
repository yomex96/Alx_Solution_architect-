# Guided Lab: Creating a VPC

## 🎯 Lab Overview and Objectives

This lab demonstrates how to deploy a **Virtual Private Cloud (VPC)** and associated resources in AWS, without dealing with physical networking complexity.

After completing the lab, you will be able to:

* ✅ Deploy a VPC
* ✅ Create a **public subnet**
* ✅ Create a **private subnet**
* ✅ Create and attach an **Internet Gateway (IGW)**
* ✅ Launch an **application server** to test the VPC

---

## 🕒 Duration

Approximately **30 minutes**.

---

## 🛠️ Task 1: Creating a VPC

1. Open **VPC Console** → **Your VPCs** → **Create VPC**.
2. Configure:

   * Name tag: `Lab VPC`
   * IPv4 CIDR: `10.0.0.0/16`
3. Click **Create VPC**.
4. Optional: Add **Tags** for organization (cost center, environment).
5. Enable DNS hostnames: **Actions → Edit VPC settings → Enable DNS hostnames**.

> EC2 instances launched in this VPC will now automatically get DNS hostnames.

---

## 🛠️ Task 2: Creating Subnets

### Task 2.1: Public Subnet

1. **Subnets → Create subnet**.
2. Configure:

   * VPC ID: `Lab VPC`
   * Subnet name: `Public Subnet`
   * Availability Zone: First AZ
   * IPv4 CIDR: `10.0.0.0/24`
3. Enable **Auto-assign Public IPv4**: `Actions → Edit subnet settings`.

### Task 2.2: Private Subnet

1. **Create subnet**:

   * VPC ID: `Lab VPC`
   * Subnet name: `Private Subnet`
   * Availability Zone: First AZ
   * IPv4 CIDR: `10.0.2.0/23`

> Public subnet will later connect to the Internet via an IGW; private subnet remains isolated.

---

## 🛠️ Task 3: Creating an Internet Gateway (IGW)

1. **Internet Gateways → Create internet gateway**

   * Name tag: `Lab IGW`
2. Attach IGW to VPC: `Actions → Attach to VPC → Lab VPC`.

> Internet Gateway allows communication between the VPC and the Internet.

---

## 🛠️ Task 4: Configuring Route Tables

1. Rename default route table → `Private Route Table`.
2. Create **Public Route Table**:

   * Name: `Public Route Table`
   * VPC: `Lab VPC`
3. Add route to IGW:

   * Destination: `0.0.0.0/0`
   * Target: `Lab IGW`
4. Associate public subnet with **Public Route Table** → makes it internet-accessible.

> Steps to make a subnet public: Create IGW → Route table → Add 0.0.0.0/0 → Associate subnet.

---

## 🛠️ Task 5: Creating a Security Group for the Application Server

1. **Security Groups → Create security group**

   * Name: `App-SG`
   * Description: `Allow HTTP traffic`
   * VPC: `Lab VPC`
2. Add inbound rule:

   * Type: HTTP
   * Source: Anywhere (0.0.0.0/0)

> Security group acts as a firewall for your EC2 instance.

---

## 🛠️ Task 6: Launching an Application Server in the Public Subnet

1. **EC2 → Launch instance**

   * Name: `App Server`
   * AMI: Amazon Linux 2023
   * Instance type: t2.micro
   * Key pair: `vockey`
   * Network settings: VPC → `Lab VPC`, Subnet → `Public Subnet`
   * Security group: `App-SG`
   * IAM Role: `Inventory-App-Role`
2. Add **User data script** (installs Apache, PHP, MariaDB, and the inventory app).
3. Launch instance.
4. Wait for `2/2 checks passed`.
5. Access the app using **Public IPv4 DNS**.

> Successful display of `Please configure Settings to connect to database` confirms correct VPC configuration.

---

## ✅ Conclusion

You have successfully:

* Deployed a VPC
* Created a **public subnet**
* Created a **private subnet**
* Created and attached an **Internet Gateway**
* Launched an **application server** in the public subnet

---

This lab sets the foundation for building **secure, internet-accessible architectures** in AWS with both public and private resources.
