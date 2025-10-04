## **Lab Overview**

In this lab, you explored **IAM users, groups, and policies**, added users to groups, and verified the permissions that they inherit.

**Objectives:**

1. Explore pre-created IAM users and groups.
2. Inspect IAM policies applied to groups.
3. Add users to groups following a real-world scenario.
4. Locate and use the IAM console sign-in URL.
5. Test the effects of policies on service access.

**Duration:** ~40 minutes
**AWS Restrictions:** Limited permissions for lab accounts; “not authorized” errors are expected in some cases.

---

## **Task 1: Explore Users, Groups, and Policies**

**Users created for the lab:**

* `user-1`
* `user-2`
* `user-3`

**Steps:**

1. Navigate to IAM → Users.
2. Inspect each user:

   * `user-1`: No permissions, no groups, has a console password.
3. Navigate to IAM → User groups.
4. Inspect each group:

   * **EC2-Support:** Managed policy `AmazonEC2ReadOnlyAccess`. Allows view-only access to EC2, ELB, CloudWatch, Auto Scaling.
   * **S3-Support:** Managed policy `AmazonS3ReadOnlyAccess`. Allows Get/List for all S3 resources.
   * **EC2-Admin:** Inline policy `EC2-Admin-Policy`. Allows viewing EC2 info and starting/stopping instances.

**IAM Policy Structure:**

* **Effect:** Allow or Deny
* **Action:** AWS API actions (e.g., `ec2:DescribeInstances`)
* **Resource:** Scope of resources (* means all)

---

## **Task 2: Add Users to Groups**

**Business scenario:** Assign users based on job function:

| User   | Group       | Permissions                       |
| ------ | ----------- | --------------------------------- |
| user-1 | S3-Support  | Read-only access to S3            |
| user-2 | EC2-Support | Read-only access to EC2           |
| user-3 | EC2-Admin   | Start/Stop and view EC2 instances |

**Steps:**

1. **user-1 → S3-Support:** Add user to group; inherits `AmazonS3ReadOnlyAccess`.
2. **user-2 → EC2-Support:** Add user to group; inherits `AmazonEC2ReadOnlyAccess`.
3. **user-3 → EC2-Admin:** Add user to group; inherits `EC2-Admin-Policy`.

**Verification:**

* Check Users column in IAM → User groups; each group should show 1 user.

---

## **Task 3: Sign in and Test Permissions**

### **Step 3.1: Get IAM Sign-In URL**

* IAM Dashboard → “Sign-in URL for IAM users” (e.g., `https://123456789012.signin.aws.amazon.com/console`)
* Copy URL to a text editor for use.

---

### **Step 3.2: Test user-1 (S3 Support)**

* Sign in using `user-1` credentials.
* Test **S3 access:** Can list buckets and view contents.
* Test **EC2 access:** Access denied (correct behavior).

---

### **Step 3.3: Test user-2 (EC2 Support)**

* Sign in using `user-2` credentials.
* Test **EC2 access:** Can view instances, but cannot stop/start (read-only).
* Test **S3 access:** Access denied (correct behavior).

---

### **Step 3.4: Test user-3 (EC2 Admin)**

* Sign in using `user-3` credentials.
* Test **EC2 access:** Can view and stop/start instances (full admin rights for EC2).
* Test **S3 access:** Access denied (correct behavior).

---

## **Key Concepts Learned**

1. **IAM Users:** Individual identities with login credentials.
2. **IAM Groups:** Collection of users with common permissions.
3. **Policies:** Define allowed/denied actions on AWS resources.

   * **Managed policies:** Predefined, reusable.
   * **Inline policies:** Custom, attached to a single user/group.
4. **Inheritance:** Users inherit permissions from groups.
5. **Testing permissions:** Sign in as IAM users to verify access.
6. **Security best practice:** Grant permissions **based on roles**, not individuals.

---

## **Lab Outcome**

* Explored IAM users and groups.
* Inspected and understood AWS-managed and inline policies.
* Added users to groups according to a business scenario.
* Verified permissions using the IAM sign-in URL.
* Demonstrated principle of least privilege in action.


