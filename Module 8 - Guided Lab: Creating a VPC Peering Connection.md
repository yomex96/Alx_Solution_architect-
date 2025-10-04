

# Guided Lab: Creating a VPC Peering Connection

## 🎯 Lab Overview and Objectives

In this lab, you will learn how to securely connect two Virtual Private Clouds (VPCs) using **VPC Peering**. This enables private communication between resources in different VPCs.

By the end of this lab, you will be able to:

* ✅ Create a VPC peering connection
* ✅ Configure route tables for communication across VPCs
* ✅ Enable **VPC Flow Logs** for monitoring
* ✅ Test connectivity across the peered VPCs
* ✅ Analyze flow logs to understand traffic patterns

---

## 🕒 Duration

This lab takes about **30 minutes** to complete.

---

## 🛠️ Task 1: Creating a VPC Peering Connection

A **VPC Peering Connection** allows traffic between two VPCs as if they were in the same network.

Steps:

1. Open **VPC Management Console**.
2. In the left menu, select **Peering Connections** → **Create Peering Connection**.
3. Configure:

   * **Name (optional):** `Lab-Peer`
   * **VPC ID (Requester):** Lab VPC
   * **VPC ID (Accepter):** Shared VPC
4. Click **Create**.
5. From **Actions → Accept request**, accept the peering request.

---

## 🛠️ Task 2: Configuring Route Tables

Next, configure routing so traffic between VPCs goes through the peering connection.

* **Lab VPC → Shared VPC**

  1. Go to **Route Tables**.
  2. Select **Lab Public Route Table**.
  3. Edit routes → **Add route**:

     * Destination: `10.5.0.0/16` (CIDR of Shared VPC)
     * Target: **Peering Connection → Lab-Peer**

* **Shared VPC → Lab VPC**

  1. Select **Shared-VPC Route Table**.
  2. Edit routes → **Add route**:

     * Destination: `10.0.0.0/16` (CIDR of Lab VPC)
     * Target: **Peering Connection → Lab-Peer**

✅ Both VPCs can now route traffic to each other.

---

## 🛠️ Task 3: Enabling VPC Flow Logs

Enable **Flow Logs** to monitor traffic between VPCs.

1. Go to **Your VPCs** → Select **Shared VPC**.
2. Under **Flow logs** tab → **Create flow log**.
3. Configure:

   * **Name:** `SharedVPCLogs`
   * **Aggregation interval:** 1 minute
   * **Destination:** CloudWatch Logs
   * **Log Group:** `ShareVPCFlowLogs`
   * **IAM Role:** `vpc-flow-logs-Role`
4. Click **Create flow log**.

---

## 🛠️ Task 4: Testing the VPC Peering Connection

Now, test connectivity between **Inventory App (Lab VPC)** and **Database (Shared VPC)**.

1. Access the Inventory app via `EC2PublicIP` (from AWS Details).
2. In the app, go to **Settings** and configure:

   * Endpoint: (DB endpoint from AWS Details)
   * Database: `inventory`
   * Username: `admin`
   * Password: `lab-password`
3. Save → The app should now pull data from the database.

✅ This proves the connection works (database has no internet gateway, so traffic must go through peering).

---

## 🛠️ Task 5: Analyzing VPC Flow Logs

Inspect CloudWatch logs to verify network traffic.

1. Open **ShareVPCFlowLogs** log group.
2. View the log stream (`eni-*`).
3. Look for traffic patterns, especially on **port 3306 (MySQL)**.

Example log fields:

* **Account ID** – AWS account hosting the VPC
* **Network Interface** – `eni-*` identifier
* **Source IP** – EC2 instance (app server)
* **Destination IP** – RDS instance (database)
* **Port 3306** – Database traffic
* **Action** – Accept/Reject
* **Status** – OK

---

## ✅ Conclusion

You have successfully:

* Created a **VPC Peering Connection**
* Configured **Route Tables** for bidirectional communication
* Enabled **Flow Logs** for visibility
* Tested app-to-database communication
* Analyzed **network traffic logs**

Your architecture now supports private, secure cross-VPC communication.


