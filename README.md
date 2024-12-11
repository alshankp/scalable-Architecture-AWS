# scalable-Architecture-AWS
# Auto Scaling Group with Application Load Balancer and Apache Setup

This project demonstrates how to set up an **Auto Scaling Group (ASG)** with an **Application Load Balancer (ALB)** on AWS, using an Apache web server. It covers creating a custom Virtual Private Cloud (VPC), subnets, and scaling policies, with a focus on simplicity and reproducibility for beginners.

---

## **Table of Contents**

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Architecture Diagram](#architecture-diagram)
- [Step-by-Step Setup](#step-by-step-setup)
  - [1. VPC and Networking](#1-vpc-and-networking)
  - [2. EC2 Instance with Apache](#2-ec2-instance-with-apache)
  - [3. Create an AMI from EC2 Instance](#3-create-an-ami-from-ec2-instance)
  - [4. Create Target Group](#4-create-target-group)
  - [5. Create Application Load Balancer](#5-create-application-load-balancer)
  - [6. Create Launch Template](#6-create-launch-template)
  - [7. Create Auto Scaling Group](#7-create-auto-scaling-group)
- [Testing the Setup](#testing-the-setup)
- [Final Thoughts](#final-thoughts)

---

## **Overview**

In this setup, we:

1. Create a custom VPC with public and private subnets in two availability zones.
2. Deploy an Apache web server on an EC2 instance.
3. Create an AMI (Amazon Machine Image) from the configured instance.
4. Use the AMI to create an Auto Scaling Group (ASG).
5. Integrate the ASG with an Application Load Balancer (ALB).
6. Configure Route 53 for DNS to make the web server accessible via a custom domain name.

---

## **Prerequisites**

- An AWS account.
- Basic understanding of AWS services such as EC2, VPC, and Route 53.
- Domain name registered (e.g., via Freenom or any other provider).
- AWS CLI installed (optional for manual steps).

---

## **Architecture Diagram**

Here’s a high-level architecture diagram of the setup:

```
Internet
   |
Application Load Balancer (Public Subnets)
   |
Auto Scaling Group (Private Subnets)
   |
EC2 Instances (Apache Web Server)
   |
RDS (Optional, in Private Subnets)
```

---

## **Step-by-Step Setup**

### **1. VPC and Networking**

1. **Create a VPC:**
   - Go to the AWS VPC console.
   - Create a VPC with a CIDR block (e.g., `10.0.0.0/16`).

2. **Create Subnets:**
   - Create 4 subnets: 2 public and 2 private across 2 availability zones (e.g., `10.0.1.0/24`, `10.0.2.0/24`, etc.).

3. **Set up an Internet Gateway:**
   - Attach an Internet Gateway to the VPC.
   - Update the route table for public subnets to include a route to the Internet Gateway.

4. **Private Subnets:**
   - Ensure private subnets do not have a route to the Internet Gateway.

### **2. EC2 Instance with Apache**

1. **Launch an EC2 instance in the public subnet:**
   - Choose Ubuntu 22.04 as the AMI.
   - Assign a security group allowing inbound HTTP (port 80) and SSH (port 22).

2. **Install Apache:**
   ```bash
   sudo apt update
   sudo apt install apache2 -y
   echo "<h1>Welcome to Apache Server</h1>" | sudo tee /var/www/html/index.html
   ```

3. **Verify Apache:**
   - Access the instance’s public IP in a browser to see the Apache default page.

### **3. Create an AMI from EC2 Instance**

1. **Stop the EC2 instance.**
2. **Create an AMI:**
   - Right-click the instance in the EC2 console and select `Create Image`.
   - Provide a name and description for the AMI.

### **4. Create Target Group**

1. **Go to Target Groups in EC2 console.**
2. Create a target group with the following settings:
   - Target type: Instances.
   - Protocol: HTTP.
   - Port: 80.
   - Health check path: `/`.

### **5. Create Application Load Balancer**

1. **Go to Load Balancers in EC2 console.**
2. Create an Application Load Balancer:
   - Choose internet-facing.
   - Add the public subnets from both AZs.
   - Assign a security group that allows HTTP (port 80).
   - Attach the target group created earlier.

### **6. Create Launch Template**

1. **Go to Launch Templates in EC2 console.**
2. Create a launch template with the following:
   - Select the AMI created earlier.
   - Choose an instance type (e.g., t2.micro).
   - Assign the security group that allows HTTP traffic.

### **7. Create Auto Scaling Group**

1. **Go to Auto Scaling Groups in EC2 console.**
2. Create an Auto Scaling Group:
   - Use the launch template created earlier.
   - Select the private subnets for instance launch.
   - Attach the target group created earlier.
   - Configure scaling policies based on CPU utilization or other metrics.

---

## **Testing the Setup**

1. Verify that the Auto Scaling Group launches instances in private subnets.
2. Access the Application Load Balancer’s DNS name in the browser to view the Apache webpage.
3. Test the scaling policy by simulating high CPU load (e.g., `stress` tool).

---

## **Final Thoughts**

This project demonstrates the power of AWS Auto Scaling and Load Balancers to create a scalable, fault-tolerant web application. Feel free to extend this setup by adding:

- **RDS Integration:** Host a database in private subnets.
- **HTTPS:** Add SSL certificates using AWS ACM.
- **Logging and Monitoring:** Enable CloudWatch for monitoring and alarms.

Contributions are welcome! If you find this project helpful, give it a ⭐ and share your feedback.

---

## **License**

This project is licensed under the MIT License.
