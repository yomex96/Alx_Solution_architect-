
## Overview

# ALX AWS Academy – Cloud Architecting Capstone Project


Here’s a **summary README** for your repo based on all the files you listed, with a **cloud computing and AWS focus**:

## 📌 Overview

This repository contains all labs, challenges, templates, and data files for the **AWS Academy Cloud Architecting 3 – Capstone Project**.

It demonstrates how to design, build, and secure **scalable, highly available, and serverless applications** using AWS services.
The work covers **VPC design, RDS migration, IAM security, serverless architecture, CloudFormation automation, and hybrid storage** — following AWS best practices.

The project focuses on **Infrastructure as Code (IaC), cloud architecture best practices, and automation**.

---
## 📂 Major Projects

### 1. **Highly Available PHP Web Application on AWS**

A real-world capstone challenge to migrate a PHP + MySQL application from a **single EC2 instance** into a **highly available, secure AWS environment**.

#### Objectives

* ✅ Deploy a PHP app in **Auto Scaling Group** across private subnets.
* ✅ Host MySQL in **Amazon RDS** with private access only.
* ✅ Secure credentials with **AWS Secrets Manager**.
* ✅ Distribute traffic using an **Application Load Balancer (ALB)**.
* ✅ Achieve fault tolerance with **Multi-AZ architecture**.

#### Why It Matters

* **RDS** ensures managed backups, scaling, and security.
* **ALB + Auto Scaling** provides elasticity and high availability.
* **Secrets Manager** eliminates hardcoded credentials.
* **Private subnets** protect backend systems.

📖 Reference: *AWS Academy Final Project*



## Project Modules and Highlights

### **Module 3 – Identity and Access Management**

* Guided Lab: Exploring **AWS IAM**.
* Hands-on experience creating IAM users, groups, roles, and policies.

### **Module 4 – Static Website**

* Challenge Lab: Creating a **static website** for the cafe.
* Demonstrates S3 hosting and public access configurations.

### **Module 5 – Dynamic Website & File Systems**

* Challenge Lab: Creating a **dynamic website**.
* Guided Lab: Introducing **Amazon EFS** for shared file storage.

### **Module 6 – Database Migration**

* Guided Lab: Creating an **Amazon RDS database**.
* Challenge Lab: Migrating a database to **RDS** using sample SQL data (`Countrydatadump.sql`).

### **Module 7 – Networking**

* Guided Lab: Creating a **Virtual Private Cloud (VPC)**.
* Challenge Lab: Creating a **VPC networking environment** for the cafe.

### **Module 8 – VPC Peering**

* Guided Lab: Establishing **VPC peering** for cross-VPC communication.

### **Module 9 – Security**

* Guided Lab: Encrypting **data at rest** with AWS encryption options.
* Securing a web application using **Amazon Cognito**.

### **Module 10 – High Availability**

* Guided Lab: Creating a **highly available environment**.
* Challenge Lab: Implementing **scalable and fault-tolerant infrastructure** for the cafe.

### **Module 11 – Infrastructure Automation**

* Guided Lab: Automating infrastructure with **AWS CloudFormation**.
* Challenge Lab: Deploying and updating stacks (`cafe-network.yaml`, `cafe-app.yaml`).

### **Module 13 – Decoupled Architecture**

* Guided Lab: Building **decoupled architecture** using **Amazon SQS** for messaging.

### **Module 14 – Serverless & Microservices**

* Guided Labs: Implementing **serverless architecture** using **AWS Lambda**.
* Breaking a monolithic application into **microservices** for scalability.

### **Module 16 – Hybrid Storage**

* Configuring hybrid storage and migrating data using **AWS Storage Gateway (S3 File Gateway)**.

---

### 2. **Scalable & Highly Available Cafe App**

* Designed an end-to-end cafe environment with **multi-tier architecture**.
* Deployed **web, application, and database layers** across multiple Availability Zones.
* Automated deployment with **CloudFormation** (`cafe-network.yaml`, `cafe-app.yaml`).
* Added **Amazon SQS decoupling** and **serverless Lambda microservices**.
* Migrated SQL data (`Countrydatadump.sql`) into **Amazon RDS**.

📖 Reference: *Module 10–14 Labs & Challenges*

---

### 3. **Automation with CloudFormation**

* Automated infrastructure deployments for networking and app layers.
* Used CloudFormation templates (`cafe-network.yaml`, `cafe-app.yaml`) for **repeatable IaC**.
* Demonstrated **blue/green deployment** and **infrastructure updates** without downtime.

📖 Reference: *Module 11 Labs*

---

### 4. **Security & Identity**

* Configured **IAM users, groups, and policies**.
* Implemented **Cognito authentication** for web apps.
* Enabled **encryption at rest** with AWS KMS.

📖 Reference: *Modules 3 & 9 Labs*

---

### 5. **Serverless & Microservices**

* Converted a **monolithic app into microservices**.
* Used **Lambda functions** for compute.
* Integrated **Amazon SQS** for message queuing.
* Designed a **fully serverless backend** for scale and cost efficiency.

📖 Reference: *Module 13 & 14 Labs*

---

### 6. **Hybrid Storage & Data Migration**

* Configured **AWS Storage Gateway (S3 File Gateway)**.
* Migrated on-premise style workloads into AWS cloud storage.

📖 Reference: *Module 16 Lab*

---

## 🏗️ Key Learnings

* **Infrastructure as Code (IaC):** Automating deployments with CloudFormation.
* **Scalability & High Availability:** Multi-AZ deployments, load balancing, auto scaling.
* **Security Best Practices:** IAM, Cognito, encryption, and private subnets.
* **Serverless & Decoupling:** Lambda, SQS, microservices design.
* **Hybrid Cloud:** Extending on-premises storage with AWS gateways.

---

## 📂 Repo Contents

* `CafeWebServer.zip` → Sample cafe web app.
* `Countrydatadump.sql` → SQL data for migration.
* `cafe-network.yaml`, `cafe-app.yaml` → CloudFormation templates.
* `AWS Academy Cloud Architecting 3 – Capstone Project.md` → Project overview.
* Module-specific lab instructions (`.md` files).
* `README.md` → You are here.



---

## Files of Interest

* **Templates:** `cafe-network.yaml`, `cafe-app.yaml` – CloudFormation templates for network and app layers.
* **SQL Data:** `Countrydatadump.sql` – Sample database for migration exercises.
* **Project Documentation:**

  * `AWS Academy Cloud Architecting 3 – Capstone Project.md` – Project overview.
  * Module-specific guided and challenge lab instructions (`.md` files).
* **Web Server:** `CafeWebServer.zip` – Sample application for lab exercises.

---

## Key Learnings

* **Infrastructure as Code (IaC):** Using **CloudFormation templates** for repeatable and automated deployments.
* **Scalability & High Availability:** Building **fault-tolerant architectures** with multiple layers.
* **Security:** Implementing **IAM roles, encryption, and Cognito authentication**.
* **Serverless & Microservices:** Breaking monolithic apps into **lambda functions** and using **SQS for decoupling**.
* **Hybrid Storage & Migration:** Integrating **on-premises data** with AWS using Storage Gateway.

---


Perfect — that project fits nicely into your repo and actually shows the **capstone level real-world scenario**. I’ll extend the README summary to include it under **Major Projects**. Here’s the updated version in **Markdown**:

---




