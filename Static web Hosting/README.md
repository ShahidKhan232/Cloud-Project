# AWS S3 Static Website Hosting with CloudFront - README

## Overview

This project provides a step-by-step guide to hosting a static website on AWS S3 with CloudFront for improved performance, security (HTTPS).

---

## Steps to Host a Static Website on AWS S3 with CloudFront

### **Step 1: Create an S3 Bucket**

1. Log in to the **AWS Management Console**.
2. Navigate to **S3** and click **Create bucket**.
3. Enter a unique bucket name (e.g., `my-static-website`).
4. **Uncheck** "Block all public access" to allow public file access.
5. Enable **Static Website Hosting**:
   - Go to the **Properties** tab.
   - Scroll to **Static website hosting** and select **Enable**.
   - Set `index.html` as the **default root object**.
6. Click **Create bucket**.

### **Step 2: Upload Website Files to S3**

1. Open the newly created S3 bucket.
2. Click **Upload** → Select `index.html`, `style.css`, `script.js`, images, etc.
3. Click **Upload** to confirm.

### **Step 3: Make Files Public**

1. Select all uploaded files.
2. Click **Actions** → **Make public** (or set permissions manually).
3. Add a **Bucket Policy** to allow public access:
   - Go to **Permissions** → **Bucket Policy**.
   - Add the following policy (replace `BUCKET_NAME` with your bucket name):
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:GetObject",
           "Resource": "arn:aws:s3:::BUCKET_NAME/*"
         }
       ]
     }
     ```
4. Click **Save changes**.

### **Step 4: Test Your Website**

- Go to **Properties** → **Static Website Hosting**.
- Copy the **Endpoint URL** (e.g., `http://BUCKET_NAME.s3-website-region.amazonaws.com`).
- Open it in a browser to verify your website is live.

---

## **Step 5: Set Up CloudFront for HTTPS & Performance**

1. Open the **CloudFront Console** and click **Create Distribution**.
2. **Origin Settings**:
   - **Origin Domain Name**: Select your S3 bucket.
   - **Origin Access**: Choose **Public or Private** (OAI recommended for security).
3. **Default Cache Behavior**:
   - Set **Viewer Protocol Policy** to `Redirect HTTP to HTTPS`.
4. **Distribution Settings**:
   - Set **Default Root Object** to `index.html`.
   - Click **Create Distribution**.
5. **Copy the CloudFront URL** (e.g., `https://d1234abcd.cloudfront.net`).
6. Now, your website is securely served with HTTPS!

---

## **Final Verification**

- Visit your **CloudFront URL** or custom domain to verify everything works.
- Your static website is now live with **AWS S3 + CloudFront + HTTPS**! 🚀

---

### **Congratulations! 🎉 Your static website is now live and optimized!**

