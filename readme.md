![AWS VPC Architecture](architecture/vpc-example-private-subnets.png)


AWS Production-Style VPC with Public & Private Subnets, Bastion Host, and Application Load Balancer

A hands-on AWS networking project built using the AWS Management Console to understand core networking concepts before moving to Infrastructure as Code (Terraform).

Project Overview

This project demonstrates how to build a production-style AWS network architecture from scratch using the AWS Console.

The objective was to understand:

Amazon VPC
Public & Private Subnets
Route Tables
Internet Gateway
Bastion Host
Security Groups
EC2 Networking
Application Load Balancer (ALB)
Target Groups
High Availability across Multiple Availability Zones

The project follows:

AWS Official Documentation
Abhishek Veeramalla's AWS Series (Video #7)
🏗 Architecture
Internet
│
│
Internet Gateway
│
┌─────────────┴─────────────┐
│ │
Public Subnet 1A Public Subnet 1B
│
Bastion Host (Ubuntu)
│
SSH (Private IP)
│
┌───────────┴───────────┐
│ │
Private Subnet 1A Private Subnet 1B
│ │
Ubuntu + Nginx Ubuntu + Nginx
│ │
└───────────┬───────────┘
│
Target Group
│
Application Load Balancer
│
Browser Requests
🚀 Services Used
Amazon VPC
EC2
Internet Gateway
Route Tables
Security Groups
Bastion Host
Application Load Balancer
Target Group
Ubuntu Server
Nginx
🌍 Region
US East (N. Virginia)
📂 Network Configuration
VPC
Property Value
Name first-prod-style-aws-vpc-project
CIDR 10.0.0.0/16
Subnets
Subnet Type AZ CIDR
Public Subnet 1A Public us-east-1a 10.0.0.0/24
Private Subnet 1A Private us-east-1a 10.0.1.0/24
Public Subnet 1B Public us-east-1b 10.0.2.0/24
Private Subnet 1B Private us-east-1b 10.0.3.0/24
🌐 Internet Gateway

One Internet Gateway attached to the VPC.

Public Route Table:

Destination : 0.0.0.0/0
Target : Internet Gateway
🔐 Bastion Host

A Bastion Host was deployed in the Public Subnet to securely access EC2 instances running in Private Subnets.

Purpose
SSH Entry Point
Administrative Access
Secure Private Network Access
🖥 EC2 Instances

Two Ubuntu EC2 instances were deployed inside Private Subnets.

Each instance runs:

Ubuntu Server
Nginx
Custom HTML Page

Instance pages display:

Instance Number
Hostname
Different Theme Colors

This makes it easy to verify Load Balancer traffic distribution.

⚖ Application Load Balancer

Configured with:

Internet Facing
HTTP Listener (Port 80)
Target Group
Health Checks

Health Check Configuration

Property Value
Protocol HTTP
Path /
Success Code 200
Healthy Threshold 5
Unhealthy Threshold 2
Timeout 5 Seconds
Interval 30 Seconds
🔐 Security Groups
Bastion Host

Inbound

SSH (22) → My Public IP
Web Servers

Inbound

HTTP (80)
SSH (22) from Bastion Host
✅ Features Implemented
Custom VPC
Public & Private Subnets
Multi-AZ Architecture
Internet Gateway
Route Tables
Bastion Host
Ubuntu EC2
Nginx Web Server
Application Load Balancer
Target Groups
Health Checks
Load Balancing
Secure SSH Access
🧪 Testing Performed
SSH Connectivity
Laptop → Bastion Host
Bastion Host → Private EC2
Web Server Testing

Verified:

curl localhost

on both instances.

Load Balancer Testing

Opened ALB DNS in browser.

Observed:

Instance 1

↓

Refresh

↓

Instance 2

↓

Refresh

↓

Instance 1

Traffic alternated correctly between both EC2 instances.

🐞 Challenges Faced & Solutions

1. Wrong SSH Username
   Problem
   Permission denied (publickey)
   Cause

Used:

ec2-user

instead of

ubuntu
Solution

Connected using:

ssh -i Test.pem ubuntu@<Public-IP> 2. Python HTTP Server
Problem
400 Bad Request

under Load Balancer traffic.

Cause

Python HTTP server is not designed for production workloads.

Solution

Installed and configured Nginx.

3. Nginx Serving Default Page
   Problem

Custom HTML was not displayed.

Cause

The file wasn't properly written.

Solution

Used:

sudo tee /var/www/html/index.html > /dev/null <<EOF

<html>
...
</html>
EOF
4. Target Group Health Check Failure
Problem

One instance stayed unhealthy.

Cause

Target registered on port:

8000

instead of

80
Solution

Re-registered instance on port 80.

5. Security Group & VPC Mismatch
   Problem

Auto Scaling Group creation failed due to Security Group and VPC mismatch.

Cause

Security Group belonged to a different VPC.

Solution

Created a new Security Group in the correct VPC and updated the Launch Template.

📚 Key Learnings

This project provided practical experience with:

AWS Networking Fundamentals
VPC Design
CIDR Planning
Public vs Private Networking
High Availability
Bastion Host Architecture
EC2 Administration
Linux Server Management
Nginx Deployment
Load Balancing
Health Checks
AWS Troubleshooting
Security Group Design
🚀 Future Improvements
Add NAT Gateway
Auto Scaling Group
Launch Template
HTTPS using ACM
Route 53
CloudWatch Monitoring
AWS Systems Manager Session Manager
Infrastructure as Code using Terraform
CI/CD Deployment Pipeline
