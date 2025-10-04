**“Configuring Hybrid Storage and Migrating Data with AWS Storage Gateway S3 File Gateway”** lab, including the tasks, concepts, and key outcomes:



## **Lab Overview**

In this lab, you configured **AWS Storage Gateway S3 File Gateway** to connect an on-premises Linux server to Amazon S3. You then migrated data, set up **cross-Region replication**, and applied **S3 lifecycle policies**.

**Objectives:**

1. Configure an S3 File Gateway with an NFS file share and attach it to a Linux instance.
2. Migrate data from a Linux instance to an S3 bucket.
3. Create primary and secondary S3 buckets for cross-Region replication.
4. Configure S3 lifecycle policies.

**Duration:** ~90 minutes
**AWS Restrictions:** Limited permissions for lab accounts; follow instructions to avoid errors.

---

## **Task 1: Reviewing Lab Architecture**

* **Linux EC2 instance** (emulating on-prem server) in **US East (N. Virginia) `us-east-1`**.
* **S3 File Gateway** deployed in the same region as the Linux server.
* **Primary S3 bucket** (source) in **US East (Ohio) `us-east-2`**.
* **Secondary S3 bucket** (destination) in **US West (Oregon) `us-west-2`**.
* **Cross-Region replication** from primary to secondary bucket.

---

## **Task 2: Creating S3 Buckets**

1. Open **S3 Console** → **Create bucket**.
2. **Primary bucket (source):**

   * Region: US East (Ohio) `us-east-2`
   * Enable **Bucket Versioning**
   * Provide unique bucket name → Create bucket.
3. **Secondary bucket (destination):**

   * Region: US West (Oregon) `us-west-2`
   * Enable **Bucket Versioning**
   * Provide unique bucket name → Create bucket.

---

## **Task 3: Enabling Cross-Region Replication**

1. In the **source bucket**, go to **Management → Replication rules → Create replication rule**.
2. Configure:

   * Rule name: `crr-full-bucket`
   * Status: Enabled
   * Apply to **all objects**
   * Destination: Choose secondary bucket (`us-west-2`)
   * IAM role: `S3-CRR-Role`
3. Upload a small test file to the source bucket.
4. Verify replication in the destination bucket (may take a few minutes).

---

## **Task 4: Configuring the S3 File Gateway**

1. Open **Storage Gateway Console → Create gateway**:

   * Gateway type: **Amazon S3 File Gateway**
   * Host platform: **Amazon EC2 → Launch instance**
2. EC2 instance configuration:

   * AMI: `aws-storage-gateway`
   * Instance type: `t2.xlarge`
   * Key pair: `vockey`
   * VPC/Subnet: `On-Prem-VPC` / `On-Prem-Subnet`
   * Security groups: `FileGatewayAccess` + `OnPremSshAccess`
   * Storage: Add 150 GiB volume
3. Launch instance → Wait for status **2/2 checks passed**
4. Activate gateway in Storage Gateway console using **public IPv4 address**
5. Configure file share:

   * Connect to **source S3 bucket**
   * Access via **NFS**
   * Storage class: **S3 Standard**
   * IAM role: Use **FgwIamPolicyARN**
6. Status changes to **Available** → Copy mount command for Linux instance.

---

## **Task 5: Mounting the File Share and Migrating Data**

1. Connect to **Linux instance** via Session Manager.
2. View data: `ls /media/data` (20 `.png` files)
3. Create mount directory:

   ```bash
   sudo mkdir -p /mnt/nfs/s3
   ```
4. Mount NFS share:

   ```bash
   sudo mount -t nfs -o nolock,hard <NFS-IP>:/lab-nfs-bucket /mnt/nfs/s3
   ```
5. Verify mount: `df -h`
6. Copy data to NFS share:

   ```bash
   cp -v /media/data/*.png /mnt/nfs/s3
   ```

---

## **Task 6: Verifying Data Migration**

1. Check **source bucket** in `us-east-2`: Confirm 20 image files.
2. Check **destination bucket** in `us-west-2`: Confirm replicated files (may take up to 15 minutes).

---

## **Key Concepts Learned**

1. **S3 File Gateway:** Enables hybrid cloud storage for on-premises workloads.
2. **NFS Mount:** Allows Linux servers to read/write files to S3 buckets as local directories.
3. **Cross-Region Replication:** Ensures data durability and disaster recovery.
4. **S3 Lifecycle Policies:** Automates data management (transition, archival, or deletion).

---

## **Lab Outcome**

* Configured **S3 File Gateway** with NFS.
* Migrated data from Linux instance to **S3 bucket**.
* Created **primary and secondary buckets** for replication.
* Verified **cross-Region replication**.
* Understood hybrid storage integration in AWS.

