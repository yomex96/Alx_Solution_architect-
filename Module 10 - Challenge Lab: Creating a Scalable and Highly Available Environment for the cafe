# Challenge Lab: Scalable and Highly Available Café Application

## 🎯 Objectives

* Design and deploy a **scalable** and **highly available** architecture for the café’s web application.
* Ensure the system can handle **spikes in traffic** (tens of thousands of users).
* Distribute requests across **multiple servers and Availability Zones (AZs)**.
* Implement **Auto Scaling** and **Elastic Load Balancing (ELB)**.

---

## ✅ Steps Breakdown

### **Task 1: Inspect the Environment**

* Explore **VPC, subnets, security groups**.
* Answer questions like:

  * Which ports are open in `CafeSG`?
  * Can instances in **public subnets** be reached from the internet?
  * Can instances in **private subnets** access the internet (via NAT)?
  * What AMI (`Cafe WebServer Image`) is available?

---

### **Task 2: Networking Across Multiple AZs**

* Create a **NAT Gateway** in the **second public subnet**.
* Update **route tables**:

  * Private Subnet 1 → NAT Gateway 1.
  * Private Subnet 2 → NAT Gateway 2.

This ensures **instances in private subnets** can reach the internet for updates and patches.

---

### **Task 3: Create a Launch Template**

* **Name:** e.g., `CafeWebTemplate`.
* **AMI:** `Cafe WebServer Image`.
* **Instance Type:** `t2.micro`.
* **Key Pair:** Create & download new key.
* **Security Group:** `CafeSG`.
* **Tags:**

  * Key: `Name`
  * Value: `webserver`.
* **IAM Role:** `CafeRole`.

---

### **Task 4: Create an Auto Scaling Group**

* **Name:** `CafeASG`.
* **Launch Template:** Use the one created above.
* **VPC & Subnets:** Private Subnet 1 + Private Subnet 2.
* **Group Size:**

  * Desired: `2`.
  * Min: `2`.
  * Max: `6`.
* **Scaling Policy:**

  * Metric: Average CPU utilization.
  * Target Value: `25%`.
  * Warmup: `60s`.

Result → 2 instances always running, auto-scales under load.

---

### **Task 5: Create a Load Balancer**

* **Type:** Application Load Balancer (ALB).
* **Name:** `CafeALB`.
* **Subnets:** Public Subnet 1 + Public Subnet 2.
* **Security Group:** New SG allowing HTTP (port 80) from anywhere.
* **Target Group:** New, for `CafeASG` instances.
* **Register Targets:** Link Auto Scaling Group → ALB Target Group.

Result → Distributes traffic to private EC2 instances.

---

### **Task 6: Testing**

#### 6.1 Test Without Load

* Access **ALB DNS Name** + `/cafe`.
* Confirm web app loads correctly.

#### 6.2 Test Auto Scaling Under Load

* Use **AWS Systems Manager Session Manager** to connect to a web server instance.
* Run:

  ```bash
  sudo amazon-linux-extras install epel
  sudo yum install stress -y
  stress --cpu 1 --timeout 600
  ```
* Observe **new EC2 instances** being created automatically in the Auto Scaling Group.

---

## 📊 Final Architecture

* **2 Private Subnets** running café app servers (min 2 instances).
* **2 Public Subnets** hosting NAT Gateways + ALB.
* **ALB** routes external traffic → private app servers.
* **Auto Scaling Group** ensures scaling & high availability.
* **CafeRole IAM role** provides instance permissions.

---

## ✍️ Deliverables

1. **Architecture Diagram** showing:

   * VPC, Public + Private Subnets across 2 AZs.
   * NAT Gateways in each AZ.
   * ALB → ASG → EC2 Web Servers.
2. **Deployed Café Web Application** (accessible via ALB DNS).
3. **Written Summary** of design decisions:

   * High Availability → multiple AZs + ALB.
   * Scalability → Auto Scaling Group.
   * Security → private EC2 servers, public only for ALB.

