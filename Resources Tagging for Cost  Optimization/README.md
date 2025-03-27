# AWS Resource Tagging for Cost Optimization

## Overview

This guide provides a step-by-step approach to implementing AWS resource tagging for cost optimization. Proper tagging ensures cost allocation, organization, security, and automation.

---

## 1️⃣ Define a Tagging Strategy

### **Why Tagging Matters**

AWS tags are key-value pairs that help with:

- Cost tracking and allocation
- Resource organization
- Security and compliance
- Automation and lifecycle management

### **Recommended Tags**

| Tag Key     | Purpose                         | Example Values              |
| ----------- | ------------------------------- | --------------------------- |
| Environment | Identify resource lifecycle     | Dev, Staging, Production    |
| Project     | Associate with a project        | EcommerceApp, DataPipeline  |
| CostCenter  | Map to business cost centers    | Marketing, Finance          |
| Owner       | Assign ownership accountability | JohnDoe, DevOpsTeam         |
| Application | Define resource application     | CRM, HRMS                   |
| Department  | Classify by department          | Engineering, IT, Operations |

### **Tagging Standards**

- Use a **consistent format** (e.g., `CamelCase` or `snake_case`).
- **Avoid free-text values**; use predefined options where possible.
- Ensure **all critical resources** are tagged at creation.

---

## 2️⃣ Apply Tags to AWS Resources

### **Using AWS Console**

1. Open **AWS Management Console**.
2. Navigate to the service (e.g., **EC2**, **S3**).
3. Select a resource and go to the **Tags** tab.
4. Click **Manage Tags > Add Tag**.
5. Enter the **Key-Value Pair**.
6. Click **Save**.

### **Using AWS CLI**

#### Tag an EC2 Instance:

```sh
aws ec2 create-tags --resources i-1234567890abcdef0 --tags Key=Environment,Value=Production Key=Owner,Value=JohnDoe
```

#### Tag an S3 Bucket:

```sh
aws s3api put-bucket-tagging --bucket my-bucket-name --tagging 'TagSet=[{"Key":"Environment","Value":"Production"}]'
```

#### List Tags for an EC2 Instance:

```sh
aws ec2 describe-tags --filters "Name=resource-id,Values=i-1234567890abcdef0"
```

### **Using AWS SDK (Boto3 - Python)**

```python
import boto3

ec2 = boto3.client('ec2')

ec2.create_tags(
    Resources=['i-1234567890abcdef0'],
    Tags=[
        {'Key': 'Environment', 'Value': 'Production'},
        {'Key': 'Owner', 'Value': 'JohnDoe'}
    ]
)
```

### **Bulk Tagging Using AWS Tag Editor**

1. Open **AWS Resource Groups & Tag Editor**.
2. Select **Regions** and **Resource Types**.
3. Click **Search resources** to find untagged resources.
4. Select multiple resources and click **Manage Tags**.
5. Apply missing tags.

---

## 3️⃣ Enforce Tagging Compliance

### **Using AWS Organizations SCP**

Deny untagged EC2 instances:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "aws:RequestTag/Environment": ["Dev", "Staging", "Production"]
                }
            }
        }
    ]
}
```

### **Using AWS Config**

1. Open **AWS Config**.
2. Click **Rules > Add Rule**.
3. Select **required-tags** rule.
4. Define **required tags** (e.g., `Project`, `CostCenter`).
5. Click **Save**.

---

## 4️⃣ Enable AWS Cost Allocation Tags

### **Activating Cost Allocation Tags**

1. Open **AWS Billing Dashboard**.
2. Click **Cost Allocation Tags**.
3. Enable **User-defined tags**.
4. Click **Activate** (Processing takes **24 hours**).

### **Analyze Costs in AWS Cost Explorer**

1. Open **AWS Cost Explorer**.
2. Click **Filters** and select **Tag Key**.
3. View **spending trends** based on tags.

---

## 5️⃣ Automate Resource Tagging

### **Auto-Tagging with AWS Lambda and CloudTrail**

#### **Lambda Function for Auto-Tagging EC2 Instances**

```python
import json
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    
    for record in event['Records']:
        if record['eventName'] == 'RunInstances':
            instance_id = record['responseElements']['instancesSet']['items'][0]['instanceId']
            user = record['userIdentity']['arn']
            
            ec2.create_tags(
                Resources=[instance_id],
                Tags=[{'Key': 'Owner', 'Value': user}]
            )
    
    return {"status": "success"}
```

### **Configure CloudTrail to Trigger Lambda**

1. Open **AWS CloudTrail**.
2. Create a **new trail**.
3. Enable logging for **EC2 RunInstances**.
4. Set **Lambda function** as a destination.

---

## 6️⃣ Audit and Optimize Regularly

### **Find Untagged Resources Using AWS Resource Groups**

1. Open **AWS Resource Groups**.
2. Filter by **untagged resources**.
3. Apply missing tags using **AWS Tag Editor**.

### **Set Up AWS Budgets Based on Tags**

1. Open **AWS Budgets**.
2. Create a **New Budget**.
3. Use **filters** to track costs by `Project`, `Department`, or `CostCenter`.
4. Set up **alerts** for budget limits.

---

## ✅ Summary

### **1️⃣ Define a Tagging Strategy**

- Identify critical tags (`Environment`, `Owner`, `CostCenter`).
- Establish naming conventions.

### **2️⃣ Apply Tags**

- Use AWS Console, CLI, Boto3, and AWS Tag Editor.

### **3️⃣ Enforce Tagging Compliance**

- Implement **SCPs** to deny untagged resources.
- Use **AWS Config** for monitoring.

### **4️⃣ Track Costs Using AWS Cost Explorer**

- Activate **Cost Allocation Tags**.
- Use **AWS Cost Explorer** for insights.

### **5️⃣ Automate Tagging**

- Use **AWS Lambda + CloudTrail** for auto-tagging.

### **6️⃣ Audit and Optimize Regularly**

- Use **AWS Resource Groups** to find untagged resources.
- Set up **AWS Budgets** to track spending per tag.
