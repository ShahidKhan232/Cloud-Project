# Auto-Scaling for Peak Traffic Hours (AWS)

## Overview
This guide provides a step-by-step implementation of **Auto-Scaling** on AWS to handle peak traffic efficiently. The solution involves **Amazon EC2 Auto Scaling, Elastic Load Balancer (ELB), CloudWatch Alarms, and Scaling Policies**.

---

## Prerequisites
Ensure you have the following:
- An **AWS Account**
- IAM permissions to manage **EC2, Load Balancer, Auto Scaling, and CloudWatch**
- A registered **domain** (optional, for Route 53)

---

## Step 1: Set Up an Elastic Load Balancer (ELB)
1. Go to **AWS Console** → **EC2 Dashboard** → **Load Balancers**
2. Click **"Create Load Balancer"**, choose **Application Load Balancer (ALB)**
3. Configure the following:
   - **Internet-facing**
   - **Listeners:** HTTP (80) or HTTPS (443)
   - **Security Group:** Open required ports
4. Create a **Target Group** and enable **Health Checks**
5. Register any existing instances
6. Complete the setup and note the **ELB DNS Name**

---

## Step 2: Create an Auto Scaling Group
### 1️⃣ Create a Launch Template
1. Go to **EC2 Dashboard** → **Launch Templates**
2. Click **"Create Launch Template"**
3. Configure:
   - **AMI ID:** Amazon Linux 2 or Ubuntu
   - **Instance Type:** `t3.medium`
   - **Security Group:** Allow HTTP, HTTPS, SSH
   - **User Data (Optional startup script)**
   ```bash
   #!/bin/bash
   yum update -y
   yum install httpd -y
   systemctl start httpd
   systemctl enable httpd
   echo "Welcome to Auto-Scaling Server" > /var/www/html/index.html
   ```
4. Click **Create Launch Template**

### 2️⃣ Create the Auto Scaling Group
1. **Go to EC2 Dashboard → Auto Scaling Groups**
2. Click **"Create Auto Scaling Group"**
3. Select the **Launch Template**
4. Configure scaling settings:
   - **Min Instances:** `2`
   - **Desired Capacity:** `2`
   - **Max Instances:** `6`
5. Attach the **Load Balancer**
6. Enable **Health Checks**
7. Save & **Create Auto Scaling Group**

---

## Step 3: Implement Scaling Policies
### 1️⃣ Scheduled Scaling (For Predictable Traffic)
1. **Go to Auto Scaling Group → Scaling Policies → Scheduled Actions**
2. Click **"Create Scheduled Action"**
3. Set the schedule:
   - **Scale Up (8 AM UTC):** Min: `3`, Desired: `4`, Max: `6`
   - **Scale Down (10 PM UTC):** Min: `1`, Desired: `2`, Max: `4`

### 2️⃣ Dynamic Scaling (CPU-Based Auto-Scaling)
1. **Go to Auto Scaling Group → Scaling Policies**
2. Click **"Create Scaling Policy"**
3. **Configure Scale-Out (Add Instance)**
   - **Metric:** CPU Utilization
   - **Threshold:** CPU > 70% for 5 minutes
   - **Action:** Add `1` instance
4. **Configure Scale-In (Remove Instance)**
   - **Threshold:** CPU < 30% for 5 minutes
   - **Action:** Remove `1` instance

---

## Step 4: Implement Auto-Scaling Triggers
1. **Go to AWS CloudWatch → Alarms → Create Alarm**
2. Set up alarms for CPU utilization:
   - **CPU > 70%** → Trigger **scale-out**
   - **CPU < 30%** → Trigger **scale-in**

---

## Step 5: Test Auto-Scaling
✅ **Simulate High Traffic** using Apache JMeter or Locust:
   ```sh
   ab -n 10000 -c 100 http://YOUR-LOAD-BALANCER-DNS
   ```
✅ **Observe scaling** in **EC2 → Auto Scaling Group**
✅ **Simulate Low Traffic** and verify instance termination

---

## Step 6: Monitor & Optimize
1. **Go to CloudWatch → Metrics → EC2 Auto Scaling**
2. Check:
   - CPU Usage Trends
   - Instance Lifecycle (scale in/out events)
   - Billing Reports
3. **Optimize Policies:**
   - Adjust thresholds based on real traffic patterns
   - Use custom AMIs for faster instance boot time
   - Implement **Lifecycle Hooks** for graceful shutdown

---

## Summary
| Step | Action |
|------|--------|
| 1 | Configure ELB for load balancing |
| 2 | Create Auto Scaling Group & Launch Template |
| 3 | Implement Scheduled Scaling for peak traffic |
| 4 | Implement Dynamic Scaling based on CPU load |
| 5 | Set up CloudWatch Alarms for scaling triggers |
| 6 | Test auto-scaling using load simulation |
| 7 | Monitor & optimize scaling policies |

---
