**step-by-step summary** of your **Guided Lab: Introducing Amazon EFS**:

## **Lab Objective**

* Learn how to create and use **Amazon EFS**.
* Mount EFS on an EC2 instance.
* Examine performance using benchmarking and CloudWatch.

---

## **Task 1: Create a Security Group for EFS**

1. Go to **EC2 → Security Groups**.
2. Copy the Security Group ID of `EFSClient`.
3. Create a new security group:

   * **Name:** EFS Mount Target
   * **Description:** Inbound NFS access from EFS clients
   * **VPC:** Lab VPC
4. Add an **Inbound rule**:

   * **Type:** NFS
   * **Source:** Custom → paste `EFSClient` Security Group ID
5. Create the security group.

---

## **Task 2: Create an EFS File System**

1. Go to **EFS → Create file system → Customize**
2. **Step 1:**

   * Disable automatic backups
   * Lifecycle management → Transition into IA: None
   * Tags: `Name → My First EFS File System`
3. **Step 2:**

   * VPC: Lab VPC
   * Detach default security group for each AZ
   * Attach `EFS Mount Target` security group for each AZ
4. **Step 3 & 4:**

   * Review configuration → Create
5. Wait for **File system state → Available** and **Mount target state → Available** (2–3 mins)

---

## **Task 3: Connect to EC2 Instance**

1. Use **Systems Manager Session Manager** → copy `InstanceSessionURL` from AWS Details.
2. Open in a browser tab → connect to EC2 instance.

---

## **Task 4: Mount the EFS File System**

1. Install utilities:

```bash
sudo su -l ec2-user
sudo yum install -y amazon-efs-utils
```

2. Create a mount directory:

```bash
sudo mkdir efs
```

3. In EFS console → **My First EFS File System → Attach → Using the NFS client**
4. Run the mount command (example):

```bash
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-xxxxxxx.efs.us-west-2.amazonaws.com:/ efs
```

5. Confirm mount:

```bash
sudo df -hT
```

* Check type (`nfs4`) and mount path (`/home/ec2-user/efs`)

---

## **Task 5: Examine Performance**

### **Write Performance using fio**

```bash
sudo fio --name=fio-efs --filesize=10G --filename=./efs/fio-efs-test.img --bs=1M --nrfiles=1 --direct=1 --sync=0 --rw=write --iodepth=200 --ioengine=libaio
```

* Review summary for WRITE performance

### **Monitor EFS via CloudWatch**

1. Go to **CloudWatch → All Metrics → EFS → File System Metrics**
2. Check **PermittedThroughput**

   * Peak should be around 3 GB/s
3. Check **DataWriteIOBytes**

   * Use Sum and 1-minute period
   * Divide peak bytes by 60 → approximate write throughput in B/s

**Notes:**

* EFS scales throughput with storage size
* File systems larger than 1 TiB burst to 100 MiB/s per TiB
* Throughput is shared across all EC2 instances connected

---

## **Conclusion**

* Logged in to AWS Console
* Created **Amazon EFS file system**
* Mounted it to an **EC2 instance**
* Examined and benchmarked performance using **fio** and **CloudWatch**

