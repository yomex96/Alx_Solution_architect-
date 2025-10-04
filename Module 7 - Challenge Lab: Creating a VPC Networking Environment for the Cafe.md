

### **Lab Summary: Architecture and Tasks**

#### **Scenario**

* The café moved its database to a private subnet (RDS).
* They want the application server in **another private subnet**.
* Access to the private subnet is via a **bastion host** in a public subnet.
* Private subnet instances need **internet access** (for updates) via a **NAT gateway**.

---

### **Key Tasks**

#### **1. Create a Public Subnet**

* **VPC:** Lab VPC
* **Subnet Name:** Public Subnet
* **AZ:** us-east-1a (example)
* **CIDR:** 10.0.0.0/24
* **Internet Gateway:** Create and attach to Lab VPC
* **Route Table:** Add route `0.0.0.0/0 → IGW` and associate with public subnet

---

#### **2. Create a Bastion Host**

* **Instance Name:** Bastion Host
* **AMI:** Amazon Linux 2023
* **Instance Type:** t2.micro
* **Key Pair:** vockey
* **Subnet:** Public Subnet
* **Security Group:** Bastion Host SG

  * **Inbound:** SSH (22) from **My IP**
  * **Outbound:** default (allow all)

---

#### **3. Test Bastion Host**

* Download **SSH key** (labuser.pem or labuser.ppk)
* Connect using SSH:

  ```bash
  ssh -i labuser.pem ec2-user@<bastion-public-IP>
  ```

---

#### **4. Create a Private Subnet**

* **Subnet Name:** Private Subnet
* **CIDR:** 10.0.1.0/24
* **AZ:** Same as public subnet

---

#### **5. Create a NAT Gateway**

* **Name:** Lab NAT Gateway
* **Subnet:** Public Subnet
* **Elastic IP:** Allocate new
* **Private Route Table:**

  * Attach to private subnet
  * Route `0.0.0.0/0 → NAT Gateway`

---

#### **6. Create EC2 Instance in Private Subnet**

* **Instance Name:** Private Instance
* **AMI:** Amazon Linux 2023
* **Instance Type:** t2.micro
* **Key Pair:** vockey2
* **Subnet:** Private Subnet
* **Security Group:** Private Instance SG

  * **Inbound:** SSH (22) from **Bastion Host SG**
  * **Outbound:** default (allow all)

---

#### **7. SSH Passthrough Setup**

* **Windows:** Use **Pageant** with both vockey.ppk and vockey2.ppk, enable agent forwarding
* **macOS/Linux:** Use `ssh-agent` and add both keys:

  ```bash
  ssh-add -K vockey.pem
  ssh-add -K vockey2.pem
  ssh -A ec2-user@<bastion-public-IP>
  ssh ec2-user@<private-IP-of-private-instance>
  ```

---

#### **8. Test SSH from Bastion to Private Instance**

* Connect to bastion host
* SSH to private instance
* Verify internet connectivity:

  ```bash
  ping 8.8.8.8
  ```

---

#### **9. Create a Custom Network ACL**

* **Name:** Lab Network ACL
* **Attach to:** Private Subnet
* **Rules:**

  * Default deny all
  * Allow traffic to/from private subnet
* **Test ICMP:** Create **Test Instance** in public subnet, ping from private instance
* Modify ACL to **deny ICMP to test instance** to block traffic

---

### **Multiple Choice Answers**

1. **What is the purpose of the internet gateway in the public subnet?**
   **Answer:** Allows instances in the public subnet to communicate with the internet.

2. **What allows the instance in the private subnet to connect to the internet?**
   **Answer:** NAT Gateway in the public subnet.

3. **Can the instance in the private subnet be accessed directly from the internet?**
   **Answer:** No.

4. **Why do you use two different key pairs to access the private instance and the bastion host?**
   **Answer:** For better security, separating access credentials for public-facing and private resources.

5. **Can the bastion host use ping and get a reply from the instance in the private subnet?**
   **Answer:** Yes, if ICMP is allowed in security groups or ACLs.

6. **Which security group rules allow the private EC2 instance to receive the return traffic when it pings the test instance?**
   **Answer:** Outbound rules allowing ICMP traffic (default allow all).

---

✅ **Lab Complete:**

* VPC created
* Public subnet with internet access
* Private subnet with NAT gateway
* Bastion host allows secure SSH access
* Private EC2 instance secured
* Network ACL provides extra traffic control


