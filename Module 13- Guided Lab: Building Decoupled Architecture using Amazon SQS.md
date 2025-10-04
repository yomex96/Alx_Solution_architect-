
# Guided Lab: Building Decoupled Applications Using Amazon SQS

## **Lab Overview**

This lab focuses on building modern, **decoupled applications** using **Amazon SQS** and **SNS**. Decoupling improves application performance, reliability, and scalability by separating tightly coupled components into independent services that communicate asynchronously.

### **Objectives**

* Review how the image processing web application works.
* Configure **Amazon S3 bucket events** to send messages to an **SNS topic**.
* Configure **Amazon SQS** to subscribe to an SNS topic and store messages.
* Implement **polling** to consume messages from an SQS queue.
* Use **Amazon SNS** to send email notifications.

### **Duration**

~60 minutes

---

## **Scenario**

You work with an image processing application that creates tinted images. The lab has **two phases**:

1. **Phase 1 – Tightly Coupled Architecture**

   * Web server communicates **synchronously** with the application server.
   * Issues: If one component fails, the whole application fails.

2. **Phase 2 – Decoupled Architecture**

   * Web server writes to **S3** and sends notifications via **SNS**.
   * **SQS** queues messages for the application server to process asynchronously.
   * Benefits: Improved scalability, availability, and fault tolerance.

---

## **Phase 1: Tightly Coupled Application**

### **Tasks**

1. **Install Image Processing Application**

   * Open **AWS Cloud9 IDE (Phase1Cloud9url)**
   * Deactivate AWS managed temporary credentials.
   * Download and install:

     ```bash
     wget <code.zip URL>
     unzip code.zip
     cd phase_1/web_server_1/
     npm install
     cd ../app_server_1/
     npm install
     ```
2. **Configure Security Group & IAM**

   * Open **EC2 → Phase1Server → Security Groups**
   * Add inbound rule: **Custom TCP 8000-8100** from Anywhere-IPv4
   * Attach **Ec2InstanceProfile IAM role**
3. **Configure S3 Bucket Permissions**

   * Open **S3 → phase1bucket → Permissions**
   * Disable **Block all public access**
   * Add bucket policy allowing your **local IP** access.
4. **Configure Application**

   * **Browser tier:** `web_server_1/static/js/config.js`
     Set URLs with Phase1 EC2 Public IP and S3 bucket.
   * **Web server tier:** `web_server_1/libs/config.js`
     Set S3 bucket name and processing URL.
   * **Application server tier:** `app_server_1/libs/config.js`
     Set S3 bucket name.
5. **Start Servers**

   ```bash
   # Web server
   cd /phase_1/web_server_1/
   npm start
   # App server
   cd /phase_1/app_server_1/
   npm start
   ```
6. **Test Application**

   * Access: `http://<Phase1 EC2 Public IP>:8008`
   * Upload images and verify processing.

### **Analysis**

* **Tightly coupled architecture:** Web server waits for application server to process images.
* **Drawback:** Component failure halts the entire system.

---

## **Phase 2: Decoupled Architecture**

### **Tasks**

1. **Install Application**

   * Open **AWS Cloud9 IDE (Phase2Cloud9url)**
   * Disable temporary credentials.
   * Download and install:

     ```bash
     cd phase_2/web_server_2/
     npm install
     cd ../app_server_2/
     npm install
     ```

2. **Configure EC2**

   * Copy **Public IPv4** of Phase2Server for app configuration.

3. **Configure Amazon SQS**

   * Create **ImageApp** queue
   * Copy **Queue URL** for application server configuration.

4. **Configure Amazon SNS**

   * Create **uploadnotification** topic
   * Add S3 bucket permissions to publish messages
   * Save topic ARN for later

5. **Configure S3 Event Notifications**

   * Open **phase2bucket → Properties → Event notifications**
   * Create event:

     * Event type: **All object create events**
     * Destination: **SNS uploadnotification**

6. **Create SNS Subscriptions**

   * **SQS subscription:** ImageApp queue
   * **Email subscription:** Enter personal email → confirm

7. **Configure Application Parameters**

   * **Browser tier:** `web_server_2/static/js/config.js`
     Set EC2 IPs, S3 bucket, and app/server URLs
   * **Web server tier:** `web_server_2/libs/config.js`
     Set S3 bucket name
   * **Application server tier:** `app_server_2/libs/config.js`
     Set S3 bucket name and SQS URL

8. **Start Servers**

   ```bash
   # Web server
   cd /phase_2/web_server_2/
   npm start
   # App server
   cd /phase_2/app_server_2/
   npm start
   ```

9. **Test Application**

   * Access: `http://<Phase2 EC2 Public IP>:8007`
   * Upload images, check notifications, poll SQS, verify tinted images
   * Email notifications are received for uploads and processed images

### **Analysis**

* **Decoupled architecture:** Web server and app server work independently
* **Advantages:** Better **scaling**, **availability**, and **performance**
* **Messages remain in SQS** if app server is down, ensuring reliable processing.

---

## **Conclusion**

You have successfully:

* Reviewed image processing application
* Configured **S3 → SNS → SQS** pipeline
* Created a **decoupled architecture**
* Implemented **polling for SQS messages**
* Configured **SNS email notifications**

---

✅ **Lab Complete**

