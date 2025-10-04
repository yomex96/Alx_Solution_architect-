
# Guided Lab: Implementing a Serverless Architecture on AWS

 **concise, Markdown-formatted summary** of your guided lab on implementing a serverless architecture on AWS:

## Lab Overview

Traditional applications require pre-provisioned servers. With **AWS Lambda**, you can run code without managing servers. Lambda executes code only when triggered, and you pay only for usage.

**Goal:** Build a serverless inventory-tracking system that:

* Loads inventory files uploaded to **Amazon S3** into **DynamoDB**.
* Sends notifications via **SNS** when inventory is out of stock.
* Displays inventory in a **serverless dashboard**.

---

## Lab Objectives

* Implement serverless architecture on AWS.
* Invoke Lambda functions from **S3** and **DynamoDB**.
* Configure **SNS notifications**.
* View inventory data in a serverless dashboard.

---

## Scenario

Stores worldwide upload inventory files to S3. Workflow:

1. Upload file to **S3**.
2. **Lambda function** reads the file and inserts items into **DynamoDB**.
3. A second Lambda function is triggered via **DynamoDB Streams**.
4. If an item is out of stock, **SNS** sends an email/SMS notification.
5. Inventory dashboard (authenticated via **Cognito**) displays current stock levels.

---

## Architecture Workflow

| Step | Action                                                                             |
| ---- | ---------------------------------------------------------------------------------- |
| 1    | Upload inventory file to S3 bucket                                                 |
| 2    | S3 event triggers Lambda (`Load-Inventory`)                                        |
| 3    | Lambda inserts items into DynamoDB                                                 |
| 4    | DynamoDB stream triggers Lambda (`Check-Stock`) → SNS notification if out of stock |

---

## Task Summary

### Task 1: Create Lambda Function to Load Data

* **Function Name:** `Load-Inventory`
* **Runtime:** Python 3.8
* **Role:** `Lambda-Load-Inventory-Role` (permissions for S3 & DynamoDB)
* **Code Logic:**

  1. Download file from S3
  2. Parse CSV
  3. Insert data into DynamoDB

### Task 2: Configure S3 Event

* Create S3 bucket (unique name)
* Configure **Event Notification**:

  * Event: `All object create events`
  * Destination: Lambda → `Load-Inventory`

### Task 3: Test Data Loading

* Upload CSV files (e.g., `inventory-berlin.csv`)
* Verify DynamoDB table `Inventory` has data
* Access **Inventory Dashboard** (via provided URL)

### Task 4: Configure SNS Notifications

* Create **SNS topic**: `NoStock`
* Subscribe via **Email/SMS**
* Confirm subscription

### Task 5: Create Lambda Function for Notifications

* **Function Name:** `Check-Stock`
* **Runtime:** Python 3.8
* **Role:** `Lambda-Check-Stock-Role` (permissions to publish SNS)
* **Code Logic:**

  1. Triggered by DynamoDB Stream
  2. If `Count == 0`, publish message to `NoStock` SNS topic

### Task 6: Test End-to-End System

* Upload inventory files to S3
* Check DynamoDB for data
* Verify dashboard updates
* Receive SNS notifications for out-of-stock items

---

## Key Benefits

* **Serverless:** No servers to provision; automatic scaling.
* **Cost-efficient:** Pay only for usage.
* **Modular architecture:** Each Lambda function handles a single responsibility.
* **Real-time notifications:** SNS alerts inventory management staff immediately.

---

## Conclusion

You have successfully:

* Implemented a **serverless architecture** on AWS.
* Invoked Lambda functions from **S3** and **DynamoDB**.
* Configured **SNS** notifications for out-of-stock items.
* Viewed inventory data via a **serverless dashboard**.

