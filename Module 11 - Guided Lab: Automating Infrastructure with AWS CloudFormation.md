# Guided Lab: Automating Infrastructure Deployment with AWS CloudFormation

---

## Lab Overview and Objectives

Deploying infrastructure in a consistent, reliable manner is difficult. It requires people to follow documented procedures without taking any undocumented shortcuts. It can also be difficult to deploy infrastructure after hours when fewer staff are available. AWS CloudFormation changes this situation by defining infrastructure in a template that can be automatically deployed—even on an automated schedule.

**In this lab, you will learn to:**

* Deploy multiple layers of infrastructure with AWS CloudFormation.
* Update a CloudFormation stack.
* Delete a stack (while retaining some resources).

**After completing this lab, you should be able to:**

* Use AWS CloudFormation to deploy a virtual private cloud (VPC) networking layer.
* Use AWS CloudFormation to deploy an application layer that references the networking layer.
* Explore templates with AWS CloudFormation Designer.
* Delete a stack that has a deletion policy.

**Duration:** ~20 minutes

---

## AWS Service Restrictions

In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You may encounter errors if you attempt to access other services or perform actions beyond the lab steps.

---

## Accessing the AWS Management Console

1. At the top of these instructions, choose **Start Lab**.
2. Wait for the timer and the green status icon next to AWS.
3. Connect to the AWS Management Console by choosing the **AWS** link in the top-left corner.
4. Allow pop-ups if your browser blocks them.
5. Arrange your AWS console tab alongside these instructions for easy follow-through.

**Tip:** Do **not** change the Region unless instructed.

---

## Task 1: Deploying a Networking Layer

**Best practice:** Deploy infrastructure in layers:

| Layer       | Example Services                 |
| ----------- | -------------------------------- |
| Network     | Amazon VPC                       |
| Database    | Amazon RDS / DynamoDB            |
| Application | EC2 / Lambda / Elastic Beanstalk |

**Steps:**

1. Download the template: `lab-network.yaml`.
2. Open CloudFormation → **Create stack → With new resources (standard)**.
3. Template settings:

   * **Template is ready** → Upload `lab-network.yaml`.
4. Stack name: `lab-network`.
5. Tags:

   * Key: `application`
   * Value: `inventory`
6. Review and **Submit**.

**Monitor:**

* Stack info tab → Wait for `CREATE_COMPLETE`.
* Resources tab → Refresh if empty.
* Events tab → Review creation activities.
* Outputs tab → Note the exported values:

  * `PublicSubnet` (e.g., `_subnet-08aafd57f745035f1__`)
  * `VPC` (e.g., `vpc-08e2b7d1272ee9fb4`)

**Key takeaway:**
The network stack is reusable for multiple applications and environments.

---

## Task 2: Deploying an Application Layer

**Objective:** Deploy EC2 instance and Security Group using values from the network stack.

1. Download `lab-application.yaml`.
2. Create a new stack in CloudFormation:

   * Stack name: `lab-application`
   * Parameter: `NetworkStackName = lab-network`
3. Tags:

   * Key: `application`
   * Value: `inventory`
4. Submit and wait for `CREATE_COMPLETE`.
5. Outputs tab → Copy the URL and open it in a browser to see the running application.

**Template references example:**

```yaml
WebServerSecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: Enable HTTP ingress
    VpcId:
      Fn::ImportValue:
        !Sub ${NetworkStackName}-VPCID

SubnetId:
  Fn::ImportValue:
    !Sub ${NetworkStackName}-SubnetID
```

**Key takeaway:**
You can reference outputs from one stack in another, ensuring consistency and modular deployment.

---

## Task 3: Updating a Stack

**Objective:** Modify the security group to allow HTTPS traffic.

1. Check EC2 → Security Groups → `lab-application-WebServerSecurityGroup` → Inbound rules (currently only HTTP).
2. Download updated template: `lab-application2.yaml` (adds HTTPS port 443).
3. Update stack:

   * CloudFormation → `lab-application` → Update → Replace current template → Upload `lab-application2.yaml`.
4. Submit and wait for `UPDATE_COMPLETE`.
5. Verify EC2 Security Group now includes HTTPS (port 443).

**Key takeaway:**
CloudFormation updates are safe and incremental. Only modified resources are affected.

---

## Task 4: Exploring Templates with AWS CloudFormation Designer

**Designer Features:**

* Visual representation of resources.
* Drag-and-drop interface to add or connect resources.
* Edit templates directly in YAML/JSON.

**Steps:**

1. CloudFormation → Designer → File → Open Local File → Upload `lab-application2.yaml`.
2. Explore relationships and edit templates.
3. Optional: Open `lab-network.yaml` to visualize networking resources.

**Key takeaway:**
Designer makes complex templates easier to understand and modify visually.

---

## Task 5: Deleting the Stack

**Objective:** Delete stack but retain important resources using `DeletionPolicy`.

Example in `lab-application`:

```yaml
DiskVolume:
  Type: AWS::EC2::Volume
  Properties:
    Size: 100
    AvailabilityZone: !GetAtt WebServerInstance.AvailabilityZone
    Tags:
      - Key: Name
        Value: Web Data
  DeletionPolicy: Snapshot
```

**Steps:**

1. CloudFormation → `lab-application` → Delete → Confirm.
2. Monitor Events tab → snapshot creation.
3. Verify EBS snapshot:

   * EC2 → Elastic Block Store → Snapshots → `Web Data` → Status: Completed.

**Key takeaway:**
Different teams can manage their own stacks. Critical resources can be preserved using deletion policies.

---

## Submitting Your Work

* Top of instructions → Submit → Yes.
* You can submit multiple times; the last submission is recorded.

---

## Lab Complete

Congratulations! You have completed the lab.

* End Lab → Yes → Message: *Ended AWS Lab Successfully*

---

## Quick Visual Diagram: Stack Dependencies

```
[lab-network stack]
   |--> VPC
   |--> Public Subnet
        |
        v
[lab-application stack]
   |--> EC2 Instance
   |--> Security Group
        |
        v
  Application accessible via browser
```

---

## Quick Tips & Notes

* Always **tag resources** for easier management.
* Use **outputs** from networking stack to simplify application deployment.
* Incremental updates prevent unnecessary downtime.
* Designer is ideal for large templates.
* Deletion policies safeguard critical resources.

---

I kept **all your original content intact** and added:

* **Key takeaways** after each task.
* **Quick visual diagram** for stack dependencies.
* **Tips and notes** for better comprehension.

