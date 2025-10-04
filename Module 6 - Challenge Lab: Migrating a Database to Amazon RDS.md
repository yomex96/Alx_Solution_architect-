 **Challenge Lab: Migrating a Database to Amazon RDS**:



## **Lab Summary**

### **Objective**

* Migrate a MariaDB database from an EC2 instance to Amazon RDS.
* Ensure the café web application uses the new database.
* Improve durability, maintenance, and scalability of the database layer.

---

## **Task 1: Create an RDS Instance**

**Steps:**

1. Go to **RDS → Create Database**
2. **Engine:** MariaDB
3. **Template:** Dev/Test
4. **Settings:**

   * DB instance identifier: `CafeDatabase`
   * Master username: `admin`
   * Master password: `Caf3DbPassw0rd!` (copy/paste exactly)
5. **Instance Configuration:**

   * Class: `db.t3.micro` (burstable)
6. **Storage:**

   * Type: General Purpose SSD (gp2)
   * Allocated storage: 20 GiB
7. **Availability & Durability:** Do not create a standby instance
8. **Connectivity:**

   * VPC: Lab VPC
   * Subnet group: lab-db-subnet-group
   * Public access: No
   * Security group: dbSG (remove default SG)
   * Availability Zone: first in the list (e.g., `us-east-1a`)
   * Port: 3306
9. **Monitoring:** Disable Enhanced Monitoring
10. **Create database** → Continue to next step (no need to wait for full availability)

---

## **Task 2: Analyze Existing Café Application**

1. Go to **EC2 → Instances → CafeServer**
2. Test the application:

   * URL: `http://<public-ip-address>/cafe`
   * Place an order → check **Order History**
3. Connect to EC2 via **Systems Manager → Session Manager**
4. Run the following commands to verify environment:

```bash
sudo su
su ec2-user
whoami
cd /home/ec2-user/
```

---

## **Task 3: Export Existing Database**

1. Check MariaDB status and version:

```bash
service mariadb status
mysql --version
```

2. Retrieve the database password from **Secrets Manager → /cafe/dbPassword**
3. Connect to the MariaDB database on EC2:

```bash
mysql -u root -p
```

* Paste the password

4. Explore database:

```sql
show databases;
use cafe_db;
show tables;
select * from `order`;
select * from `order_item`;
```

5. Export data with **mysqldump**:

```bash
mysqldump --databases cafe_db -u root -p > CafeDbDump.sql
```

* Confirm the file is created: `ls` or `cat CafeDbDump.sql`

---

## **Task 4: Work with the RDS Database**

1. Confirm RDS instance is **Available**
2. Answer lab questions (via AWS Details)
3. Update security group to allow EC2 → RDS traffic on **TCP 3306**
4. Test connection to RDS from EC2:

```bash
mysql -u admin -p --host <rds-endpoint>
```

* Enter `Caf3DbPassw0rd!` when prompted

5. Confirm no data exists yet:

```sql
show databases;
```

---

## **Task 5: Import Data into RDS**

1. Import the database dump into RDS:

```bash
mysql -u admin -p --host <rds-endpoint> < CafeDbDump.sql
```

2. Confirm data is imported:

```bash
mysql -u admin -p --host <rds-endpoint>
show databases;
use cafe_db;
show tables;
select * from `order`;
```

* Should show at least 24 orders

---

## **Task 6: Connect Web Application to RDS**

1. Go to **Secrets Manager → update secret values**

   * Update **dbUrl** to RDS endpoint
   * Keep other values unchanged (currency, dbName, timeZone, showServerInfo)
2. Stop MariaDB on EC2:

```bash
sudo service mariadb stop
```

3. Test application:

   * URL: `http://<public-ip>/cafe/menu.php`
   * Place a test order → verify in **Order History**

---

## **Lab Complete**

**Outcome:**

* Café application now uses RDS MariaDB for storage
* EC2 instance no longer hosts the database → potential for cost savings
* Database is now managed, backed up, and more scalable

