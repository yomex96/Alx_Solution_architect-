**Guided Lab: Creating an Amazon RDS Database**:


## **Lab Summary**

### **Objective**

* Launch an Amazon RDS MySQL database.
* Connect an existing web application on EC2 to the database.
* Use AWS Secrets Manager for secure credential storage.

---

## **Task 1: Create an Amazon RDS Database**

**Steps:**

1. Go to **RDS** in the AWS Console → **Create database**.
2. **Engine Options:** MySQL
3. **Template:** Dev/Test
4. **Availability & Durability:** Single DB instance
5. **Settings:**

   * DB instance identifier: `inventory-db`
   * Master username: `admin`
   * Master password: `lab-password`
   * Confirm password: `lab-password`
6. **Instance configuration:**

   * Instance class: `db.t3.micro` (burstable)
7. **Storage:**

   * Type: General Purpose SSD (gp2)
   * Allocated storage: 20 GB
   * Disable storage autoscaling
8. **Connectivity:**

   * VPC: Lab VPC
   * DB subnet group: default (private subnets)
   * Security group: `DB-SG` (remove default SG)
9. **Monitoring:** Turn off Enhanced Monitoring
10. **Additional configuration:**

    * Initial database name: `inventory`
11. **Create database** → Wait for status **Available**

---

## **Task 2: Configure Web Application Communication**

1. Open the web application:

   * Copy **AppServerPublicIP** from AWS Details
   * Paste in browser → Inventory application loads (not yet connected)
2. Retrieve database endpoint:

   * Go to **RDS → Databases → inventory-db → Connectivity & Security → Endpoint**
   * Example: `inventory-db.crwxbgqad61a.rds.amazonaws.com`
3. Configure application database settings:

   * **Endpoint:** paste copied endpoint
   * **Database:** `inventory`
   * **Username:** `admin`
   * **Password:** `lab-password`
   * Click **Save**
4. Application stores credentials in **Secrets Manager** for secure access
5. Test application:

   * Add, edit, and delete inventory records
   * Ensure at least **5 inventory records** exist before submission

---

### **Key Takeaways**

* Amazon RDS eliminates the need to manually install and manage a database.
* Using **Secrets Manager** avoids hard-coded credentials in your application.
* Connecting EC2 applications to RDS allows persistent, shared storage.


