**Creating a Static Website for the Café** lab, along with the key tasks and best practices implemented. I’ve summarized it so it’s easier to follow and understand what you accomplished:


## **Scenario**

* Café owned by Frank and Martha, with Sofía and Nikhil helping.
* Café has no web presence; relies on walk-in customers.
* Goal: Build a static website to showcase desserts, coffee, and business info.
* Additional goals: Protect data, optimize costs, and enable disaster recovery (DR).

---

## **Lab Objectives**

By the end of the lab, you should be able to:

1. Host a static website using **Amazon S3**.
2. Implement **data protection** using versioning.
3. Set a **data lifecycle strategy** for cost optimization.
4. Enable **cross-Region replication** for disaster recovery.

---

## **Tasks Overview**

### **Challenge 1: Launching a Static Website**

**Task 1: Extract files**

* Download the lab zip files (`index.html` + `images` folder) to your computer.

**Task 2: Create S3 bucket for hosting**

* Region: `us-east-1`.
* Disable “Block all public access” and enable ACLs.
* Enable **Static Website Hosting**.
* Set `index.html` as the **index document**.

**Task 3: Upload content**

* Upload `index.html`, `CSS`, and `images` folders.
* Open the bucket endpoint URL in a browser to verify the website loads.

**Task 4: Create bucket policy for public read**

* Create a policy that gives **public read access** to all objects.
* This ensures uploaded objects are automatically public.

---

### **Challenge 2: Protecting Website Data**

**Task 5: Enable versioning**

* Turn on **versioning** for the bucket.
* Edit `index.html`:

  * Change `bgcolor="aquamarine"` → `"gainsboro"`
  * Change `bgcolor="orange"` → `"cornsilk"`
* Re-upload the file.
* Verify multiple versions using **Show versions** in S3.

**Best Practice:** Protects against accidental deletion or overwrite.

---

### **Challenge 3: Optimizing Costs**

**Task 6: Set lifecycle policies**

* **Rule 1:** Move previous versions to **S3 Standard-IA** after 30 days.
* **Rule 2:** Delete previous versions after 365 days.
* This ensures older versions don’t incur unnecessary costs.

**Best Practice:** Data lifecycle management for cost optimization.

---

### **Challenge 4: Enhancing Durability / Disaster Recovery**

**Task 7: Enable cross-Region replication**

* Create a **destination bucket** in a different region.
* Enable versioning on the destination bucket.
* Set **replication** from source → destination bucket using `CafeRole`.
* Verify:

  * Minor changes to `index.html` replicate to the destination.
  * Deleted versions in source do **not** delete versions in destination (if configured correctly).

**Best Practice:** Automates **disaster recovery** by replicating data across regions.

---

## **Key Architecture Practices Applied**

1. **Data Protection:** S3 Versioning to prevent accidental overwrites/deletion.
2. **Cost Optimization:** Lifecycle rules to transition older objects to infrequent access and delete expired versions.
3. **Disaster Recovery:** Cross-Region replication ensures durability in a different AWS Region.
4. **Public Access Management:** Bucket policy ensures objects are publicly readable for the static website.

---

## **Lab Outcome**

* Static website live on S3.
* Versioning protects website content.
* Lifecycle policies manage costs automatically.
* Cross-Region replication provides DR for business continuity.



