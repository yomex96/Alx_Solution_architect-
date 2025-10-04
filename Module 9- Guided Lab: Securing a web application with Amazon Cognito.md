
# Guided Lab: Securing Applications by Using Amazon Cognito

## Lab Overview and Objectives

While building web applications, user authentication and authorization can be challenging. **Amazon Cognito** makes it convenient for developers to add sign-up, sign-in, and enhanced security functionality.

In this lab, you will:

* Create an Amazon Cognito user pool.
* Add users to the user pool.
* Update the example application to use the user pool for authentication.
* Configure the Amazon Cognito identity pool.
* Update the example application to use the identity pool for authorization.

**Duration:** ~60 minutes

---

## AWS Service Restrictions

In this lab environment, access to AWS services and service actions might be restricted to the ones required. Attempting to use other services might result in errors.

---

## Scenario

You are provided with the **Birds web application**, built with:

* **Node.js server** on AWS Cloud9
* **Amazon S3 bucket** (static website hosting)

The application features:

* **Public pages**: Home, Educational pages about birds
* **Protected pages** (require authentication):

  * Sightings page (view past bird sightings)
  * Reporting page (report new sightings)
  * Administrator page (admin operations only)

Your task: Add **authentication and authorization** to secure protected pages.

---

## Architecture

### Starting Architecture

* Node.js server on Cloud9
* S3 bucket with CloudFront distribution

### Intermediate Architecture

Add an **Amazon Cognito user pool** for authentication.

**Flow:**

1. User requests a protected page.
2. Request goes to Node.js server.
3. Redirected to Cognito Managed UI.
4. Cognito authenticates user and returns access token.
5. Token validated, protected page served.
6. Content delivered via CloudFront.

### Final Architecture

Add an **Amazon Cognito identity pool** for authorization and DynamoDB access.

**Flow:**

1. User logs in via Cognito user pool.
2. Application requests admin page.
3. Cognito returns access token.
4. Identity pool exchanges token for temporary AWS credentials.
5. Application queries DynamoDB with those credentials.

---

## Accessing the AWS Management Console

1. Choose **Start Lab**.
2. Wait for environment to be ready (green circle icon).
3. Open **AWS Console** using the provided link.

---

## Tasks

### Task 1: Preparing the Lab Environment

* Connect to **Cloud9 IDE** from AWS Details.
* Run setup commands:

```bash
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/10-lab-mod9-guided-Cognito/code.zip
unzip code.zip
cd resources
. ./setup.sh
```

* Record:

  * **S3 bucket name**
  * **CloudFront distribution domain**

* Update `config.js` with CloudFront domain.

* Upload website files to S3:

```bash
cd /home/ec2-user/environment
aws s3 cp website s3://<s3-bucket>/ --recursive --cache-control "max-age=0"
```

* Start Node.js server:

```bash
cd /home/ec2-user/environment/node_server
npm start
```

---

### Task 2: Reviewing the Birds Website

* Open the CloudFront distribution domain.
* Verify:

  * **Public pages** (Birds info accessible without login).
  * **Protected pages** (Sightings, Reports, Admin require login).

---

### Task 3: Configuring the Amazon Cognito User Pool

#### Task 3.1: Creating a User Pool

* Open **Cognito → User Pools → Create**.
* Configure:

  * Name: `bird_app_client`
  * Identifier: `email`
  * Callback URL: `https://<cloudfront-domain>/callback.html`
* Enable **Authorization code grant** & **Implicit grant**.
* Save **User Pool ID**, **App Client ID**, and **Domain prefix**.

#### Task 3.2: Adding Users and Groups

* Create users:

  * **testuser** → `Lab-password1$`
  * **admin** → `Admin123$`
* Create group: `Administrators`
* Add `admin` user to group.

---

### Task 4: Updating the Application for Authentication

* Stop node server (`Ctrl+C`).
* Update `config.js` with Cognito details.
* Push files to S3.
* Update `package.json` with `USER_POOL_ID`.
* Restart node server.

---

### Task 5: Testing User Pool Integration

* Access **Sightings page** → requires login.
* Login as **testuser** → Bird sightings visible.
* Access **Admin page** → requires admin login.
* Login as **admin** → Admin page accessible.

---

### Task 6: Configuring the Identity Pool

* Open **Cognito → Identity Pools**.
* Select `bird_app_id_pool`.
* Add Cognito user pool provider (`bird_app`).
* Save **Identity Pool ID**.

---

### Task 7: Updating the Application for Authorization

* Stop node server.
* Update `config.js` with **Identity Pool ID**.
* Update `auth.js` with **User Pool ID**.
* Push files to S3.
* Restart node server.

---

### Task 8: Testing Identity Pool Integration

* Login as **admin**.
* Go to **Report page**.
* Validate temporary AWS credentials.
* Application queries **DynamoDB table** and returns row count.

---

## Conclusion

You have successfully:

* Created an **Amazon Cognito user pool**
* Added users & groups
* Secured app authentication with Cognito user pool
* Configured an **Amazon Cognito identity pool**
* Enabled DynamoDB access with temporary credentials

✅ Application now has **secure authentication & authorization**.

---

## Submitting Your Work

* Choose **Submit**.
* Review Grades and Submission Report.
* End Lab when done.

---

© 2023, Amazon Web Services, Inc. and its affiliates. All rights reserved.

