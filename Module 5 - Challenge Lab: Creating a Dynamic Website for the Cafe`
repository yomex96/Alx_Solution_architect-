**Challenge Lab: Creating a Dynamic Website for the Café**:


## **Lab Overview**

You deploy a dynamic café website on an **EC2 instance**, configure it to use a **LAMP stack**, integrate **AWS Secrets Manager** for database credentials, test the application, and then create an **AMI** to replicate the site in another AWS Region (production environment).

**Key Learning Objectives:**

* Connect to **AWS Cloud9 IDE** on an EC2 instance.
* Analyze the EC2 environment and confirm web server accessibility.
* Install a dynamic web application.
* Configure and test a database.
* Use AWS Secrets Manager for secure credentials.
* Create an **AMI** and deploy another instance in a different region.

---

## **Step 1: Analyze the existing EC2 instance**

1. Open **EC2 console** → Instances.
2. Identify the instance named `aws-cloud9-CafeWebServer-...`.
3. Answer key questions:

   * Is it in a **public subnet**?
   * Does it have an **IPv4 Public IP**?
   * Which **inbound TCP ports** are open?
   * Does it have an **IAM role**?

---

## **Step 2: Connect to Cloud9 IDE**

1. Use the `Cloud9url` from AWS Details → open in new tab.
2. Confirm you can access:

   * **Bash terminal**
   * **File browser**
   * **File editor**

---

## **Step 3: Confirm LAMP Stack & Web Server**

1. Check OS:

   ```bash
   cat /proc/version
   ```
2. Check Apache & PHP versions:

   ```bash
   sudo httpd -v
   php --version
   service httpd status
   ```
3. Start services & enable auto-start:

   ```bash
   sudo chkconfig httpd on
   sudo service httpd start
   sudo chkconfig mariadb on
   sudo service mariadb start
   sudo systemctl enable mariadb
   ```
4. Link `/var/www/` to Cloud9 workspace and set permissions:

   ```bash
   ln -s /var/www/ /home/ec2-user/environment
   sudo chown ec2-user:ec2-user /var/www/html
   ```
5. Create a **test page**:

   ```html
   <html>Hello from the café web server!</html>
   ```

   Save as `/var/www/html/index.html`.

---

## **Step 4: Install the Café Application**

1. Download and extract the app:

   ```bash
   cd ~/environment
   wget <setup.zip URL>
   unzip setup.zip
   wget <db.zip URL>
   unzip db.zip
   wget <cafe.zip URL>
   unzip cafe.zip -d /var/www/html/
   cd /var/www/html/cafe/
   wget <aws.zip URL>
   wget <aws.phar URL>
   unzip aws -d /var/www/html/cafe/
   chmod -R +r /var/www/html/cafe/
   ```
2. Configure **Secrets Manager**:

   ```bash
   cd ~/environment/setup/
   ./set-app-parameters.sh
   ```
3. Configure **database**:

   ```bash
   cd ../db/
   ./set-root-password.sh
   ./create-db.sh
   mysql -u admin -p
   # Use dbPassword from Secrets Manager
   show databases;
   use cafe_db;
   show tables;
   select * from product;
   exit;
   ```
4. Update PHP timezone and restart Apache:

   ```bash
   sudo sed -i "2i date.timezone = \"America/New_York\" " /etc/php.ini
   sudo service httpd restart
   ```
5. Test website: `http://<public-ip>/cafe`

---

## **Step 5: Test Web Application**

* Open Menu → Submit order → Check Order History.
* Confirm data is correctly saved in database.

---

## **Step 6: Create AMI & Launch in Another Region**

1. Set hostname & create SSH key:

   ```bash
   sudo hostname cafeserver
   ssh-keygen -t rsa -f ~/.ssh/id_rsa
   cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
   ```
2. Create AMI from EC2 instance:

   * **EC2 Console** → Actions → Images → Create Image → Name: `CafeServer`
3. Launch **new instance** in Oregon (us-west-2):

   * Name: `ProdCafeServer`
   * Instance type: `t2.small`
   * Key pair: Proceed without key (use generated key)
   * VPC: Lab VPC Region 2, Subnet: Public
   * Security Group: `cafeSG` → open ports 22 & 80
   * IAM Role: `CafeRole`
4. Copy new instance **Public DNS/IP**
5. Update `setup/set-app-parameters.sh` for new Region:

   ```bash
   region="us-west-2"
   publicDNS="<public-dns-of-ProdCafeServer-instance>"
   ```
6. Run the script → Verify secrets in Secrets Manager (Oregon region).

---

## **Step 7: Verify Production Instance**

1. Load website: `http://<public-ip>/cafe/`
2. Test Menu & Submit Orders.
3. Confirm functionality mirrors the development instance.

---

## ✅ **Lab Complete**

* You have successfully:

  * Connected to Cloud9 IDE.
  * Configured EC2 for web hosting.
  * Installed café app using **Secrets Manager**.
  * Tested database & application.
  * Created an AMI.
  * Deployed a production instance in a different AWS Region.

