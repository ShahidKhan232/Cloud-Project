# Serverless Application using AWS Lambda

This project demonstrates how to build a **serverless event-driven application** using **AWS Lambda**. The Lambda function is triggered when a file is uploaded to an Amazon S3 bucket.

## Prerequisites
- An **AWS Account**
- AWS CLI installed and configured
- Basic knowledge of AWS services (Lambda, S3, IAM)

## Step 1: Set Up AWS IAM Role
1. Go to the **AWS IAM Console**.
2. Create a new IAM role with **AWSLambdaBasicExecutionRole** permissions.
3. Attach **AmazonS3ReadOnlyAccess** if accessing S3.

## Step 2: Create AWS Lambda Function
1. Go to the **AWS Lambda Console**.
2. Click **Create Function** → **Author from Scratch**.
3. Set the following details:
   - **Function Name**: `myEventDrivenLambda`
   - **Runtime**: Python 3.x (or Node.js, Java, etc.)
   - **Execution Role**: Choose the IAM role created earlier.
4. Click **Create Function**.

## Step 3: Write Lambda Function Code
Use the built-in editor or upload a ZIP file. Example Python code:

```python
import json

def lambda_handler(event, context):
    print("Event received:", json.dumps(event, indent=2))
    return {
        'statusCode': 200,
        'body': json.dumps("Hello from Lambda!")
    }
```

Click **Deploy**.

## Step 4: Configure S3 Event Trigger
1. Open the **Amazon S3 Console**.
2. Select your S3 bucket.
3. Navigate to **Properties > Event Notifications**.
4. Click **Create Event Notification**:
   - **Event Name**: `S3FileUploadTrigger`
   - **Event Type**: `PUT` (new file upload)
   - **Destination**: Lambda Function → `myEventDrivenLambda`
5. Click **Save**.

## Step 5: Test the Lambda Function
1. In **Lambda Console**, click **Test**.
2. Choose **Create a new test event**.
3. Select an **S3 Put event** template.
4. Click **Test** to execute the function.

## Step 6: Monitor and Debug
- Use **Amazon CloudWatch Logs** for debugging.
- Enable **AWS X-Ray** for request tracing.
- Set up **AWS SNS** for failure notifications.

## Step 7: Deploy and Scale
- AWS Lambda automatically scales based on demand.
- Configure **Concurrency Limits** and **Provisioned Concurrency** for performance tuning.

## Security Best Practices
- Restrict **IAM roles and policies** to least privilege.
- Use **AWS Secrets Manager** for managing sensitive credentials.
- Implement **VPC and security groups** for network security.

## Conclusion
This project provides a scalable, event-driven architecture using **AWS Lambda** and **S3 triggers**. You can extend it with **DynamoDB, SQS, API Gateway**, and more for a fully serverless solution.
