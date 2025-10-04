
# Challenge Lab: Serverless Daily Sales Report for the Café


 **concise, Markdown-formatted summary** of the **Challenge Lab: Implementing a Serverless Architecture for the Café**:

## Lab Overview

The café wants **daily sales reports** to plan ingredient orders and monitor promotions. Initially, a cron job on an EC2 instance generated reports but **slowed down the web server**. To optimize cost and performance, the reporting solution is implemented using **AWS Lambda** and **Amazon SNS**.

**Goal:** Build a serverless reporting solution that generates and emails daily café sales reports.

---

## Lab Objectives

* Implement a **serverless architecture** for daily reports.
* Connect Lambda to an **RDS database** inside a **VPC**.
* Schedule Lambda execution using **EventBridge**.
* Deliver reports via **Amazon SNS email**.

---

## Scenario Workflow

1. **DataExtractor Lambda** connects to **RDS** to extract café sales data.
2. **SalesAnalysisReport Lambda** generates and sends the report via SNS.
3. **EventBridge** triggers the Lambda function **daily**.

---

## Task Summary

### Task 1: Download Source Code

* Download two `.zip` files:

  1. `salesAnalysisReportDataExtractor.zip`
  2. `salesAnalysisReport.zip`
* Extract and examine contents.

**Question:** Why does `salesAnalysisReportDataExtractor.zip` have a package folder?

---

### Task 2: Create DataExtractor Lambda in VPC

* **Security Groups:**

  * Lambda SG: `LambdaSG`
  * Update RDS security group to allow MySQL/Aurora access from `LambdaSG`
* **Lambda Function:**

  * Name: `salesAnalysisReportDataExtractor`
  * Runtime: Python 3.11
  * Role: `salesAnalysisReportDERole`
  * VPC: Lab VPC (Private subnets 1 & 2, LambdaSG)
  * Memory: 128 MB, Timeout: 30s
  * Handler: `salesAnalysisReportDataExtractor.lambda_handler`
  * Code: Upload `.zip` file

**Question:** Why must this Lambda function be connected to a VPC?

---

### Task 3: Create SalesAnalysisReport Lambda

* **Lambda Function:**

  * Name: `salesAnalysisReport`
  * Runtime: Python 3.11
  * Role: `salesAnalysisReportRole`
  * Memory: 128 MB, Timeout: 30s
  * Handler: `salesAnalysisReport.lambda_handler`
  * Code: Upload `.zip` file

---

### Task 4: Create SNS Topic

* **SNS Topic:**

  * Name: `SalesReportTopic`
  * Display Name: `Sales Report Topic`
* Add **environment variable** to Lambda:

  * `topicARN` = ARN of `SalesReportTopic`

**Question:** Can the `topicARN` be stored in **AWS Secrets Manager** instead of environment variables?

---

### Task 5: Create Email Subscription

* Subscribe your email to `SalesReportTopic`.
* Confirm subscription from your email.

**Question:** Will you receive emails if the subscription is **not confirmed**?

---

### Task 6: Test SalesAnalysisReport Lambda

* Create a test event (default `hello-world` event is fine).
* Run the Lambda function and verify that an **email report** is received.
* Troubleshoot using **CloudWatch Logs**:

  * Database connection errors → check security groups
  * Timeout errors → ensure timeout = 30s
  * Handler not found → verify correct handler

---

### Task 7: Schedule Daily Reports via EventBridge

* Create **EventBridge rule** to trigger `salesAnalysisReport` Lambda daily.
* Use **UTC time** close to your current time for testing.
* Role: `mySchedulerRole`
* Verify report delivery via email.

**Question:** How would you troubleshoot if Frank does **not receive emails**?

---

## Key Benefits

* **Serverless architecture:** No EC2 instance required, reducing cost and complexity.
* **Automated daily reports** with minimal maintenance.
* **Secure database access** via VPC and security groups.
* **Real-time notifications** via SNS email.

---

## Conclusion

You have successfully:

* Implemented a **serverless architecture** for daily café sales reporting.
* Connected **Lambda functions** to RDS within a VPC.
* Scheduled daily execution using **EventBridge**.
* Sent reports via **SNS email** to stakeholders.

