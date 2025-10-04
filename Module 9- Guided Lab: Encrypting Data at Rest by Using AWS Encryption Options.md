
# Guided Lab: Encrypting Data at Rest by Using AWS Encryption Options

## Lab Overview and Objectives

In this lab, you review the default data encryption and AWS Key Management Service (AWS KMS) encryption used to encrypt data at rest.

You will:

* Review the default encryption provided by **Amazon S3**
* Access the encrypted **Amazon S3 object**
* Create an **AWS KMS customer managed key** to encrypt and decrypt data at rest
* Create and attach an **encrypted data volume** on an existing **EC2 instance**
* Disable and re-enable an **AWS KMS key** and observe the effects on data access
* Monitor **encryption key usage** with **AWS CloudTrail event history**
* Review **key rotation**

**Duration:** ~30 minutes

---

## AWS Service Restrictions

In this lab environment, access to AWS services and actions might be restricted. Attempting to use services outside the lab scope might result in errors.

---

## Scenario

Starting architecture includes:

* An **S3 bucket** storing images
* An **EC2 instance**
* An **AWS KMS key**

By the end of the lab, you will:

* Encrypt S3 objects with **SSE-S3**
* Use a **KMS key** (`MyKMSKey`) to encrypt an **EBS volume**
* Observe **KMS and CloudTrail interactions**
* Understand **key disabling, re-enabling, and rotation**

---

## Architecture

### Beginning

* S3 bucket: stores encrypted images
* EC2 instance: runs workloads
* AWS KMS key: provisioned

### End

* **S3 object** encrypted with SSE-S3
* **Encrypted EBS volume** attached to EC2
* **KMS key** used for encryption/decryption
* **CloudTrail logs** KMS usage

---

### Encryption and Decryption Flow

1. User attaches encrypted EBS volume to EC2 instance
2. EC2 requests AWS KMS to decrypt the data key
3. AWS KMS validates permissions and returns plaintext data key
4. EC2 stores the key in memory for encryption/decryption

---

## Accessing the AWS Management Console

1. Choose **Start Lab**.
2. Wait until status icon turns **green**.
3. Choose the **AWS** link to open the console.
4. Allow pop-ups if blocked.

---

## Tasks

### Task 1: Reviewing Default Encryption for S3 Objects

1. Download **clock.png** to your computer.
2. Open **Amazon S3** → locate `imagebucket`.
3. Go to **Properties** → verify **Server-side encryption: SSE-S3**.
4. Upload `clock.png` to bucket.

   * Set **public-read** (acknowledge warning).
5. Open object properties → verify encryption settings.
6. Use **Object URL** to view the image.

✅ S3 transparently encrypts objects with **SSE-S3** and decrypts them on access.

---

### Task 2: Creating an AWS KMS Key

1. Open **AWS KMS** console → **Customer managed keys → Create key**.
2. Select **Symmetric key** → Next.
3. Alias: `MyKMSKey`.
4. Assign **Key administrators** → select `voclabs` role.
5. Assign **Key users** → select `voclabs` role again.
6. Review and **Finish**.

✅ Your KMS key is created.

---

### Task 3: Creating and Attaching Encrypted EBS Volume

1. Open **EC2 → Instances → LabInstance** → note **Availability Zone**.
2. Review root volume: **not encrypted**.
3. Create new volume:

   * Size: **1 GiB**
   * AZ: same as EC2 instance
   * Encryption: enabled with **MyKMSKey**
4. After creation → **Attach volume** to `LabInstance`.
5. Verify under **Storage tab**:

   * Root volume: not encrypted
   * Data volume: encrypted with **MyKMSKey**

✅ Encrypted volume is now securely attached.

---

### Task 4: Disabling KMS Key and Observing Effects

1. Open **KMS console** → select `MyKMSKey`.
2. **Key actions → Disable**.
3. Detach encrypted volume from EC2.
4. Attempt to re-attach volume → fails (cannot decrypt key).

📌 Reason: Disabled KMS key blocks decryption of the encrypted data key.

5. Check **CloudTrail Event history**:

   * Look for **DisableKey** event
   * Look for **AttachVolume** failure event

6. Re-enable key: **Key actions → Enable**.

7. Attach volume again → succeeds.

✅ Verified how disabling KMS affects encrypted volume access.

---

### Task 5: Analyzing KMS Activity with CloudTrail

1. Open **CloudTrail → Event history**.
2. Filter by **Event source = kms.amazonaws.com**.
3. Review events:

   * **CreateGrant** → EC2 requests permission to use KMS key
   * **Decrypt** → EC2 decrypts data key with `MyKMSKey`
   * **GenerateDataKeyWithoutPlaintext** → KMS creates encrypted data key for EBS
   * **RetireGrant** (optional)

✅ CloudTrail logs all KMS API usage for audit visibility.

---

### Task 6: Reviewing Key Rotation

1. Open **KMS console → Customer managed keys → MyKMSKey**.
2. Go to **Key rotation**.
3. Enable: **Automatically rotate this KMS key every year**.
4. Save changes.

✅ Automatic key rotation is now enabled.

---

## Conclusion

You have successfully:

* Reviewed **S3 default encryption** (SSE-S3)
* Accessed an **encrypted S3 object**
* Created a **KMS customer managed key**
* Created and attached an **encrypted EBS volume**
* Disabled and re-enabled the key to observe effects
* Monitored KMS usage via **CloudTrail**
* Configured **automatic key rotation**

Your application data at rest is now secured using **AWS-managed and customer-managed encryption options**.

---

## Submitting Your Work

1. Choose **Submit** at the top of the instructions.
2. Confirm submission.
3. Review **Grades panel** and **Submission Report**.
4. You may submit multiple times; only last submission counts.

✅ Lab Complete!

