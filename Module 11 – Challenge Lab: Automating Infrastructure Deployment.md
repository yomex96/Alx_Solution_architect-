Here’s a structured **Markdown version** of the Challenge Lab you shared, organized with headings, subheadings, and bullet points for clarity:


# Challenge Lab: Automating Infrastructure Deployment

## Scenario

The café staff have manually created AWS resources and configured applications using the AWS Management Console. While this approach worked initially, they now face challenges:

* Replicating deployments to new AWS Regions for new café locations.
* Maintaining separate development and production environments with matching configurations.

In this lab, you take the role of **Sofía** to automate the café’s deployments and replicate them to another AWS Region.

---

## Lab Overview and Objectives

In this lab, you will:

1. Create **AWS CloudFormation templates**.
2. Create and update **CloudFormation stacks**.
3. Use **AWS CodeCommit** to manage template versions.
4. Use **AWS CodePipeline** to automate stack updates.
5. Duplicate network and application resources to another AWS Region.

### Expected Outcomes

After completing the lab, you should be able to:

* Deploy a VPC networking layer using CloudFormation.
* Deploy an application layer using CloudFormation.
* Use Git with CodePipeline to create/update stacks from CodeCommit templates.
* Duplicate network and application resources in another AWS Region.

---

## Prerequisites

* The lab environment has some AWS resources already created.
* Access to AWS Management Console and Cloud9 IDE.

---

## Duration

Approximately **90 minutes**.

---

## AWS Service Restrictions

* Access may be restricted to the services required for the lab.
* Attempting actions beyond the lab instructions may result in errors.

---

## Accessing the AWS Management Console

1. Click **Start Lab** at the top of the instructions.
2. Wait until the green circle next to AWS turns green.
3. Open the AWS Management Console in a new browser tab.
4. Arrange the console and instructions side by side.

---

# Challenge 1: Creating a Static Website Using CloudFormation

### Task 1: Creating a CloudFormation Template from Scratch

1. Open **AWS Cloud9 IDE**.
2. Create a new file: `File > New File > Save As S3.yaml`.
3. Add the following:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: cafe S3 template
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
```

> **Tip:** Maintain correct indentation:
>
> * `Resources` → no indentation
> * `S3Bucket` → 2 spaces
> * `Type` → 4 spaces

4. Save the file.
5. Run the following AWS CLI commands:

```bash
aws configure get region
aws cloudformation create-stack --stack-name CreateBucket --template-body file://S3.yaml
```

6. Verify stack creation in **CloudFormation Console** and check the **S3 bucket**.

### Task 2: Configuring the Bucket as a Website

1. Download website files:

```bash
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/15-lab-mod11-challenge-CFn/s3/static-website.zip
unzip static-website.zip -d static
cd static
```

2. Set bucket ownership:

```bash
aws s3api put-bucket-ownership-controls --bucket <BUCKET-NAME> --ownership-controls Rules=[{ObjectOwnership=BucketOwnerPreferred}]
```

3. Set public access:

```bash
aws s3api put-public-access-block --bucket <BUCKET-NAME> --public-access-block-configuration "BlockPublicAcls=false,RestrictPublicBuckets=false,IgnorePublicAcls=false,BlockPublicPolicy=false"
```

4. Copy website files:

```bash
aws s3 cp --recursive . s3://<BUCKET-NAME>/ --acl public-read
```

5. Update `S3.yaml` to:

* Add deletion policy to retain the bucket.
* Configure **static website hosting** with `index.html`.
* Add **Outputs** section for the website URL.

6. Validate and update the stack:

```bash
aws cloudformation validate-template --template-body file://S3.yaml
aws cloudformation update-stack --stack-name CreateBucket --template-body file://S3.yaml
```

7. Verify that the static website works.

---

# Challenge 2: Storing Templates in a Version Control System

### Task 3: Cloning a CodeCommit Repository

1. Open **CodeCommit Console**.
2. Select repository `CFTemplatesRepo`.
3. Copy the **HTTPS clone URL**.
4. In Cloud9:

```bash
git clone <url>
cd CFTemplatesRepo
git status
```

---

# Challenge 3: Creating Network and Application Layers

### Task 4: Creating a Network Layer

1. Duplicate `template1.yaml` → `cafe-network.yaml`.
2. Update **Description**: `Network layer for the cafe`.
3. Push the template to **CodeCommit**:

```bash
git add templates/cafe-network.yaml
git commit -m 'initial commit of network template' templates/cafe-network.yaml
git push
```

4. Observe **CafeNetworkPipeline** in CodePipeline: stack should be created automatically.
5. Verify network resources in **VPC Console**.

### Task 5: Updating the Network Stack

Add outputs to `cafe-network.yaml`:

```yaml
Outputs:
  PublicSubnet:
    Description: The subnet ID to use for public web servers
    Value: !Ref PublicSubnet
    Export:
      Name: !Sub '${AWS::StackName}-SubnetID'
  VpcId:
    Description: The VPC ID
    Value: !Ref VPC
    Export:
      Name: !Sub '${AWS::StackName}-VpcID'
```

Commit and push changes to update the stack.

### Task 6: Creating the Application Stack

1. Duplicate `template2.yaml` → `cafe-app.yaml`.
2. Define **parameters**, **mappings**, and **security group**.
3. Add **EC2 instance resource**:

```yaml
CafeInstance:
  Type: AWS::EC2::Instance
  Properties:
    ImageId: !Ref LatestAmiId
    InstanceType: !Ref InstanceType
    KeyName: !FindInMap [RegionMap, !Ref "AWS::Region", keypair]
    IamInstanceProfile: CafeRole
    NetworkInterfaces:
      - DeviceIndex: '0'
        AssociatePublicIpAddress: 'true'
        SubnetId: !ImportValue
          'Fn::Sub': '${CafeNetworkParameter}-SubnetID'
        GroupSet:
          - !Ref CafeSG
    Tags:
      - Key: Name
        Value: Cafe Web Server
    UserData:
      Fn::Base64:
        !Sub |
          #!/bin/bash
          yum -y update
          yum install -y httpd mariadb-server wget
          amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
          systemctl enable httpd
          systemctl start httpd
          systemctl enable mariadb
          systemctl start mariadb
          wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/15-lab-mod11-challenge-CFn/s3/cafe-app.sh
          chmod +x cafe-app.sh
          ./cafe-app.sh
```

4. Validate and push the template to CodeCommit.
5. Observe **CafeAppPipeline** creating the application stack.
6. Test the café website using the EC2 public IP.

---

# Challenge 4: Duplicating Resources in Another AWS Region

### Task 7: Duplicating Network and Application Resources

1. Duplicate network stack in **us-west-2**:

```bash
aws cloudformation create-stack --stack-name update-cafe-network --template-body file:///home/ec2-user/environment/CFTemplatesRepo/templates/cafe-network.yaml --region us-west-2
```

2. Verify resources in **VPC Console** (Oregon region).
3. Create EC2 key pair `cafe-oregon`.
4. Upload `cafe-app.yaml` to an S3 bucket.
5. In **CloudFormation Console** (Oregon), create a new stack using the S3 template URL.
6. Set **InstanceType** parameter: `t3.micro`.
7. Verify EC2 instance, key pair, and application.

> The café application now runs in both **us-east-1** and **us-west-2** Regions using the same templates.

---

## Lab Summary

* **CloudFormation** was used to deploy a static website and a dynamic web application.
* **CI/CD pipelines** automated stack creation and updates.
* Resources were **duplicated across Regions** quickly.
* Sofía can now spin up production, test, or backup environments efficiently.

---

## Key Takeaways

* **Infrastructure as Code (IaC)** simplifies resource replication.
* **CodeCommit + CodePipeline** enables automated deployments.
* CloudFormation **outputs and imports** allow inter-stack communication.
* Templates enable **consistent configurations** across Regions.
* DevOps automation reduces errors, improves reliability, and accelerates deployment.

