
# Guided Lab: Breaking a Monolithic Node.js Application into Microservices

 **concise, organized Markdown version** of your lab guide so far. I’ve structured it for clarity, keeping headings, subheadings, and step sequences intact for easy reading and reference. You can continue adding Task 6 steps similarly.

## Overview

Traditional monolithic architectures are hard to scale. As an application's code base grows, it becomes complex to maintain, limiting innovation. Microservices architecture breaks the application into separate services, each running independently and communicating through a defined API.

In this lab, you will:

* Migrate a monolithic Node.js application to a Docker container.
* Refactor it into microservices.
* Deploy it to Amazon ECS.

**Application:** Message board where users create threads and post messages.

---

## Objectives

By the end of this lab, you will be able to:

1. Migrate a monolithic Node.js application to run in a Docker container.
2. Refactor a Node.js application to a microservices architecture.
3. Deploy a containerized Node.js microservices application to Amazon ECS.

**Duration:** ~2 hours

---

## Accessing the AWS Management Console

1. Choose **Start Lab** at the top of instructions.
2. Wait for the AWS Details panel to show a green circle (ready status).
3. Open the **AWS Management Console** via the link in the top-left corner.

---

## Task 1: Preparing the Development Environment

1. Open **AWS Cloud9** using the Cloud9 URL from the lab details.
2. Download and extract the lab files:

```bash
curl -s https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/19-lab-mod14-guided-ECS/s3/lab-files-ms-node-js.tar.gz | tar -zxv
```

**Cloud9 Folder Structure:**

* `1-no-container`: Monolithic app for standard Node.js server.
* `2-containerized-monolith`: Monolithic app ready for Docker.
* `3-containerized-microservices`: Refactored microservices version.

---

## Task 2: Running the Application on a Basic Node.js Server

### Task 2.1: Installing Required Node.js Modules

```bash
cd ~/environment/1-no-container
npm install koa koa-router
```

* Installed modules are in `node_modules`.

---

### Task 2.2: Reviewing Application Design and Code

**Files to review in `1-no-container`:**

* `node_modules` – installed modules
* `db.json` – simulated database (users, threads, posts)
* `index.js` – application entry point, handles cluster setup
* `package.json` – dependencies and entry point
* `server.js` – RESTful API definitions

**API endpoints (server.js):**

| Method | Endpoint                       | Description                     |
| ------ | ------------------------------ | ------------------------------- |
| GET    | /api/users                     | List all users                  |
| GET    | /api/users/:userId             | Get specific user info          |
| GET    | /api/threads                   | List all threads                |
| GET    | /api/threads/:threadId         | Get specific thread info        |
| GET    | /api/posts/in-thread/:threadId | List posts in a thread          |
| GET    | /api/posts/by-user/:userId     | List posts by user              |
| GET    | /api/                          | "API ready to receive requests" |
| GET    | /                              | "Ready to receive requests"     |

---

### Task 2.3: Running the Application

```bash
npm start
```

* Opens server on port `3000`.
* Open a second terminal tab to test endpoints:

```bash
curl localhost:3000/api/users
curl localhost:3000/api/users/4
curl localhost:3000/api/threads
curl localhost:3000/api/posts/in-thread/1
```

* Stop server: `Ctrl+C` in the first terminal.

---

## Task 3: Containerizing the Monolith for Amazon ECS

### Task 3.1: Preparing the Application

* Remove Node.js cluster usage; convert to single process.
* Entry point changed to `server.js`.
* Review `Dockerfile` in `2-containerized-monolith`:

```dockerfile
FROM node:alpine
WORKDIR /srv
ADD . /srv
RUN npm install
EXPOSE 3000
CMD ["node", "server.js"]
```

---

### Task 3.2: Provisioning an Amazon ECR Repository

1. Open **Elastic Container Registry** in AWS Console.
2. Create repository `mb-repo` (Private).

---

### Task 3.3: Building and Pushing the Docker Image

```bash
# Authenticate Docker with ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

# Build image
cd ~/environment/2-containerized-monolith
docker build -t mb-repo .

# Tag image
docker tag mb-repo:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-repo:latest

# Push to ECR
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-repo:latest
```

* Verify image in ECR; copy the **image URI**.

---

## Task 4: Deploying the Monolith to Amazon ECS

### Task 4.1: Creating an ECS Cluster

* Cluster name: `mb-ecs-cluster`
* EC2 instances: 2 × t2.medium, Amazon Linux 2023
* Network: Lab VPC, public subnets, existing ECSSG security group

---

### Task 4.2: Creating a Task Definition

* Task definition family: `mb-task`
* Launch type: EC2, CPU: 0.5 vCPU, Memory: 1GB
* Container: `mb-container`, port 3000, image URI from ECR

---

### Task 4.3: Creating and Deploying the Service

* Service name: `mb-ecs-service`

* Launch type: EC2

* Load balancer: Application Load Balancer, new target group `mb-target`

* Copy load balancer **DNS name** for testing.

---

### Task 4.4: Testing the Containerized Monolith

* Test REST API via browser:

```
http://<DNS-name>/api
http://<DNS-name>/api/users
http://<DNS-name>/api/threads
http://<DNS-name>/api/posts/in-thread/2
```

---

## Task 5: Refactoring the Monolith

* Break monolithic app into **microservices**:

  * Users: `/api/users/*`
  * Threads: `/api/threads/*`
  * Posts: `/api/posts/*`

### Task 5.1: Reviewing the Microservices Application

* `3-containerized-microservices/` folder contains:

  * `users/`
  * `threads/`
  * `posts/`
* Only `server.js` differs in each microservice, containing relevant REST handlers.

---

### Task 5.2: Provisioning ECR Repositories for Microservices

* `mb-users-repo`
* `mb-threads-repo`
* `mb-posts-repo`

---

### Task 5.3: Building and Pushing Images for Each Microservice

#### Users Microservice

```bash
cd ~/environment/3-containerized-microservices/users
docker build -t mb-users-repo .
docker tag mb-users-repo:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-users-repo:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-users-repo:latest
```

#### Threads Microservice

```bash
cd ~/environment/3-containerized-microservices/threads
docker build -t mb-threads-repo .
docker tag mb-threads-repo:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-threads-repo:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-threads-repo:latest
```

#### Posts Microservice

```bash
cd ~/environment/3-containerized-microservices/posts
docker build -t mb-posts-repo .
docker tag mb-posts-repo:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-posts-repo:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/mb-posts-repo:latest
```

---

### ✅ Summary So Far

* Monolith containerized and deployed to ECS.
* Microservices version split into **users**, **threads**, **posts**.
* Images built and pushed to ECR.


