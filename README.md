# Secure-Private-Container-Registry-with-Image-Lifecycle-and-Retention-Policies
This architecture shows the workflow of managing Docker images using Amazon ECR. Developers build and tag Docker images, then push them to a private ECR repository. A lifecycle policy keeps only the latest three images and automatically deletes older ones, ensuring secure and efficient container image management.

## Project Overview

This project demonstrates how to design and configure a **secure private container registry** using **Amazon Elastic Container Registry (ECR)**.

Many organizations store Docker images in public repositories without governance, which can cause:

* Security risks
* Uncontrolled access
* Storage bloat due to unused images
* Lack of lifecycle management

To solve this problem, we implemented a **private container registry with access control and automated lifecycle cleanup**.

---

# Objectives

The goal of this project is to:

* Create a **secure private container registry**
* Control access using **AWS authentication**
* Push multiple versions of Docker images
* Implement **image lifecycle management**
* Automatically delete old images to prevent storage waste

---

# Technologies Used

* **Docker**
* **Amazon ECR (Elastic Container Registry)**
* **AWS IAM**
* **AWS CLI**
* **Ubuntu EC2 Instance**

---

# Architecture

Developer → Docker Build → Tag Image → Push Image → Amazon ECR → Lifecycle Policy → Automatic Cleanup

---

# Step 1: Create Private ECR Repository

1. Login to **AWS Console**
2. Open **Elastic Container Registry (ECR)**
3. Click **Create Repository**

Configuration:

* Repository name: `my-private-repo`
* Visibility: **Private**
* Image Tag Mutability: **Mutable**
* Encryption: **AES-256**
* Scan on push: **Enabled**

Repository URI Example:

183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo

---

# Step 2: Launch EC2 Instance

Instance configuration:

* Instance Type: **t2.micro**
* OS: **Ubuntu 22.04**
* Storage: **8 GB**

Connect via SSH:

ssh -i key.pem ubuntu@<public-ip>

---

# Step 3: Install Required Tools

Update packages

sudo apt update

Install Docker

sudo apt install docker.io -y

Start Docker

sudo systemctl start docker

Install AWS CLI

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

---

# Step 4: Configure AWS CLI

aws configure

Enter:

AWS Access Key ID
AWS Secret Access Key
Region: ap-south-1
Output format: json

Verify configuration:

aws sts get-caller-identity

---

# Step 5: Authenticate Docker with ECR

aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 183192392395.dkr.ecr.ap-south-1.amazonaws.com

Expected output:

Login Succeeded

---

# Step 6: Create Docker Image

Create Dockerfile

FROM nginx
COPY . /usr/share/nginx/html

Build Docker image

docker build -t myapp:v1 .

Verify image

docker images

---

# Step 7: Tag Image for ECR

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v1

---

# Step 8: Push Image to ECR

docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v1

---

# Step 9: Push Multiple Versions

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v2
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v2

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v3
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v3

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v4
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v4

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v5
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v5

---

# Step 10: Configure Lifecycle Policy

Navigate to:

ECR → Repository → Lifecycle Policy

Create rule:

Tag status: Tagged (prefix matching)
Tag prefix: v
Match criteria: Image count
Image count: 3
Action: Expire

Lifecycle Policy JSON:

{
"rules": [
{
"rulePriority": 1,
"description": "Keep last 3 images",
"selection": {
"tagStatus": "tagged",
"tagPrefixList": ["v"],
"countType": "imageCountMoreThan",
"countNumber": 3
},
"action": {
"type": "expire"
}
}
]
}

---

# Lifecycle Policy Result

Before cleanup:

v1
v2
v3
v4
v5
v6

After lifecycle execution:

v4
v5
v6

Deleted automatically:

v1
v2
v3

This prevents **storage bloat and improves image governance**.

---

# Security Validation

Security controls implemented:

* Private container registry
* AWS authentication required
* AES-256 encryption enabled
* Image vulnerability scanning enabled
* Lifecycle policy for automatic cleanup

---

# Screenshots

1. ECR repository creation
2. Docker images pushed (v1–v6)
3. Lifecycle policy rule
4. Lifecycle policy preview

---

# Conclusion

This project demonstrates how to implement **secure container image management** using AWS ECR.

By applying lifecycle policies and access control, organizations can maintain **secure, organized, and cost-efficient container registries** while preventing unnecessary storage usage.

---

# Author

Shreyash Meshram
DevOps Internship Project
