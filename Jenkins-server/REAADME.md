# Jenkins CI/CD Pipeline with GitHub Webhook Integration

This project sets up a Jenkins CI/CD pipeline to deploy a Dockerized application on AWS EC2 instances using a declarative pipeline. The pipeline is triggered automatically whenever code is pushed to GitHub via webhook integration.

---

## Prerequisites

- **AWS Account** with EC2 instance setup
- **GitHub Repository** with the application code
- **Basic Knowledge of Linux Commands**
- **Jenkins Installed on EC2 Instance**
- **Docker Installed on EC2 Instance**

---

## Step-by-Step Implementation

### 1. Create an EC2 Instance
- Go to AWS portal and launch a new EC2 instance:
  - **Name**: jenkins-server
  - **AMI**: Ubuntu
  - **Instance Type**: t2.micro (Free Tier)
  - **Key Pair**: Create new (e.g., `docker.pem`) and download the file
  - **Allow**: HTTP, HTTPS, SSH

### 2. Connect to EC2 Instance
- Open terminal and navigate to the folder where `docker.pem` is stored
- Run the following command to SSH into the instance:
  ```sh
  ssh -i docker.pem ubuntu@<public_ip_of_ec2>
  ```

### 3. Generate SSH Keys on the Server
- Run the command:
  ```sh
  ssh-keygen
  ```
  This will generate two files:
  - `id_rsa` (Private Key)
  - `id_rsa.pub` (Public Key)

### 4. Install Jenkins
- Follow the installation steps from the official Jenkins documentation:
  [Jenkins Installation Guide](https://www.jenkins.io/doc/book/installing/linux/)
  This will also install Java automatically.

### 5. Install Docker
- Run the command to install Docker:
  ```sh
  sudo apt install docker.io -y
  ```
- Verify the installation:
  ```sh
  docker --version
  ```

### 6. Configure Security Group
- Allow **Inbound Rules** for ports **8080** (Jenkins) and **8001** (Application)
- Save the changes in AWS Security Group settings

### 7. Access Jenkins Web UI
- Open a browser and enter:
  ```
  http://<public_ip_of_ec2>:8080
  ```
- Unlock Jenkins using the command:
  ```sh
  cat /var/lib/jenkins/secrets/initialAdminPassword
  ```
- Install suggested plugins and create an admin user.

### 8. Create a Jenkins Job for CI/CD Pipeline
- Navigate to **Jenkins Dashboard > New Item**
- Choose **Freestyle Project**, name it **todo-app**, and click **OK**
- In **Source Code Management**, select **Git** and enter the repository URL
- Add credentials (if required)
- In **Build Step**, choose **Execute Shell** and add the following command:
  ```sh
  docker build -t myapp .
  docker run -d -p 8001:8001 myapp
  ```
- Click **Build Now** to trigger the build manually

### 9. Configure GitHub Webhook
- Go to **GitHub > Repo > Settings > Webhooks**
- Click **Add Webhook** and enter:
  - **Payload URL**: `http://<public_ip_of_ec2>:8080/github-webhook/`
  - **Content Type**: `application/json`
  - **Trigger Event**: Just the push event
  - Click **Add Webhook**

### 10. Enable GitHub Hook Trigger in Jenkins
- Go to **Jenkins > Configure Job**
- Under **Build Triggers**, select **GitHub hook trigger for GitScm polling**
- Save the configuration

### 11. Test the Pipeline
- Make a change in the GitHub repository and push it
- Jenkins will automatically trigger the build and deploy the updated application
- Verify by accessing the app:
  ```
  http://<public_ip_of_ec2>:8001
  ```

---

## Expected Outcome
- Every time a developer commits code to GitHub, Jenkins will automatically:
  1. Fetch the updated code
  2. Build a new Docker image
  3. Deploy a new container
  4. Make the updated application available online

---

## Conclusion
By implementing this pipeline, we achieve a fully automated CI/CD setup with GitHub webhook integration. This ensures that every code commit is immediately reflected in the live web application without manual intervention.

