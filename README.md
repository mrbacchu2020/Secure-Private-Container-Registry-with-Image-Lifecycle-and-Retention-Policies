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


<img width="1536" height="1024" alt="ChatGPT Image Mar 28, 2026, 01_04_40 PM" src="https://github.com/user-attachments/assets/6be7de9e-5ad2-43ab-abb6-d7c7253422ac" />

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
<img width="1872" height="1180" alt="project3 1" src="https://github.com/user-attachments/assets/b1dd0a2d-5235-4b4e-8d00-eaf327f88ec8" />






<img width="1871" height="1180" alt="project3 2" src="https://github.com/user-attachments/assets/1f60c18b-61d5-4829-9fa3-745def0d2692" />



---

# Step 2: Launch EC2 Instance

Instance configuration:

* Instance Type: **t2.micro**
* OS: **Ubuntu 22.04**
* Storage: **8 GB**



<img width="1878" height="1167" alt="project3 3" src="https://github.com/user-attachments/assets/50985cdb-7397-4462-91c8-52850bdbc593" />



Connect via SSH:

# ssh -i key.pem ubuntu@<public-ip>





<img width="1658" height="1166" alt="Screenshot 2026-03-13 115729" src="https://github.com/user-attachments/assets/1cbbd413-cd6f-4aa7-8b9c-b48cfd55e0a2" />









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




<img width="1864" height="1188" alt="Screenshot 2026-03-13 115829" src="https://github.com/user-attachments/assets/3862af9f-b6d4-476b-a7fe-e57ccefc9bc3" />


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



<img width="1808" height="1168" alt="Screenshot 2026-03-13 120428" src="https://github.com/user-attachments/assets/8602ae98-5772-4e32-94f0-157ab21006c3" />




<img width="1483" height="771" alt="Screenshot 2026-03-13 120737" src="https://github.com/user-attachments/assets/b33f6e48-d2d0-4de4-8bf2-9ddbaa170baf" />



---

# Step 5: Authenticate Docker with ECR

aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 183192392395.dkr.ecr.ap-south-1.amazonaws.com

Expected output:

Login Succeeded






<img width="1478" height="764" alt="Screenshot 2026-03-13 121549" src="https://github.com/user-attachments/assets/b18ea10a-600c-432a-9b06-88d54e7dc6b6" />




---

# Step 6: Create Docker Image

Create Dockerfile

FROM nginx
COPY . /usr/share/nginx/html

Build Docker image

docker build -t myapp:v1 .

Verify image

docker images


<img width="1489" height="771" alt="Screenshot 2026-03-13 121112" src="https://github.com/user-attachments/assets/15e3160e-880e-46a6-8cf5-ac0e0d144fc8" />





---

# Step 7: Tag Image for ECR

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v1




<img width="1920" height="1200" alt="Screenshot 2026-03-13 124933" src="https://github.com/user-attachments/assets/a91f7a87-4dff-4792-9ecb-9a9edf9b565f" />



---

# Step 8: Push Image to ECR

docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v1


<img width="1920" height="1200" alt="Screenshot 2026-03-13 122617" src="https://github.com/user-attachments/assets/6e3a95ae-8ecf-4773-a82e-ba0d69c5589f" />

---

# Step 9: Push Multiple Versions

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v2
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v2

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v3
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v3


<img width="1920" height="1200" alt="Screenshot 2026-03-13 123201" src="https://github.com/user-attachments/assets/b56d2f87-376a-4e18-9198-4fda097cd448" />


docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v4
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v4

docker tag myapp:v1 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v5
docker push 183192392395.dkr.ecr.ap-south-1.amazonaws.com/my-private-repo:v5



<img width="1854" height="988" alt="Screenshot 2026-03-13 123337" src="https://github.com/user-attachments/assets/897cce28-53d1-44df-a716-046581633acb" />





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

<img width="1920" height="1140" alt="Screenshot 2026-03-13 123959" src="https://github.com/user-attachments/assets/0cffb21c-b70c-4dff-aa5a-0a1b5af61dd4" />

<img width="1920" height="1200" alt="Screenshot 2026-03-13 124019" src="https://github.com/user-attachments/assets/eb984fe5-cfba-4156-8895-bebcb73114aa" />

<img width="1920" height="1140" alt="Screenshot 2026-03-13 124055" src="https://github.com/user-attachments/assets/7eb9db53-52e9-434a-94dd-5935892dacaa" />



<img width="1847" height="961" alt="Screenshot 2026-03-13 124157" src="https://github.com/user-attachments/assets/97d0f1ec-64a5-43f9-9c76-f020a710c395" />






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


<img width="1920" height="1200" alt="Screenshot 2026-03-13 123300" src="https://github.com/user-attachments/assets/5b1892f0-3b2d-4c53-a498-7df8406af664" />





---

# Security Validation

Security controls implemented:

* Private container registry
* AWS authentication required
* AES-256 encryption enabled
* Image vulnerability scanning enabled
* Lifecycle policy for automatic cleanup

---

# Conclusion

This project demonstrates how to implement **secure container image management** using AWS ECR.

By applying lifecycle policies and access control, organizations can maintain **secure, organized, and cost-efficient container registries** while preventing unnecessary storage usage.

---

# Author

Shreyash Meshram
DevOps Internship Project
