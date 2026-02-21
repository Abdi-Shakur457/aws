# AWS Highly Available Web Architecture Project
📌 Project Overview

In this project, I designed and deployed a highly available and secure web application architecture in AWS.

The solution uses:

A custom VPC spanning two Availability Zones

Public and private subnets in each AZ

Internet Gateway and NAT Gateway

Application Load Balancer (ALB)

Two private EC2 instances running Apache

Security group isolation between layers

The architecture ensures high availability, proper network segmentation, and secure access control. 


🌍 Traffic Flow
Internet
   ↓
Application Load Balancer (Public Subnets)
   ↓
Target Group
   ↓
Private EC2 Instances (AZ-A & AZ-B)

Private EC2 instances are not directly accessible from the internet.

🌐 VPC and Subnet Configuration

The VPC spans two Availability Zones to ensure high availability.


<img width="608" height="145" alt="vpc resourse ss" src="https://github.com/user-attachments/assets/c39b1705-6f80-447c-b086-2a75d19b5a85" />



<img width="865" height="114" alt="subnets ss " src="https://github.com/user-attachments/assets/27eaeeb6-b597-42e4-944a-c719e44a015c" />



🛣️ Route Tables
Public Route Table
0.0.0.0/0 → Internet Gateway



<img width="604" height="70" alt="Public route table " src="https://github.com/user-attachments/assets/88fb81f5-07e1-4494-abf9-03e2024f6512" />



Private Route Table
0.0.0.0/0 → NAT Gateway


<img width="607" height="74" alt="private route table " src="https://github.com/user-attachments/assets/528a730f-d0cd-4880-b7dc-d51a4dbef8a0" />



⚖️ Load Balancer Configuration

The ALB is internet-facing and deployed across two public subnets.

Listener: HTTP (Port 80)

Target Group: Private EC2 instances

Health Check Path: /


❤️ Target Group Health

Both private instances successfully passed health checks.


<img width="623" height="308" alt="alb ss" src="https://github.com/user-attachments/assets/5fcd222c-f92b-44d2-8aab-286a7bd4800c" />



<img width="602" height="64" alt="alb health check " src="https://github.com/user-attachments/assets/5e6f73b6-3df4-4598-8e86-101a4f5efba8" />




🔐 Security Design
ALB Security Group

Allows HTTP (Port 80) from 0.0.0.0/0

No SSH access



<img width="607" height="66" alt="alb sg " src="https://github.com/user-attachments/assets/67873d2e-944c-4bfb-a89c-bee604482b08" />


Private EC2 Security Group

Allows HTTP (Port 80) only from ALB Security Group

Allows SSH (Port 22) only from Bastion Host Security Group (if used)

No public access



<img width="600" height="99" alt="private sg rule" src="https://github.com/user-attachments/assets/30bfc5ef-fc2b-4c44-926a-395822ef4e40" />



This ensures proper isolation between the public and private layers.



🔄 Load Balancing in Action

Each private EC2 instance serves different content.

Refreshing the ALB DNS name alternates between the two instances, confirming traffic distribution.

Instance 1:



<img width="464" height="290" alt="ec2 1 working " src="https://github.com/user-attachments/assets/7d21fd1a-0d4b-4bc3-a0f4-51e076dba106" />


Instance 2:



<img width="432" height="245" alt="ec2 working " src="https://github.com/user-attachments/assets/45e4e969-125d-4105-855c-4d07bb2f606f" />



📜 User-Data Script Used

Each EC2 instance installs Apache during launch:



<img width="250" height="139" alt="ec2 1 user data " src="https://github.com/user-attachments/assets/d36cb418-a1a4-4de1-b667-16af78e34742" />




The second instance uses similar code but serves different content.


The bastion host was deployed in Availability Zone B (AZ B) within the public subnet. Its primary role was to serve as a secure access point for my private EC2 instances:

EC2 Instance 1: Located in Private Subnet A in Availability Zone A (AZ A).
EC2 Instance 2: Located in Private Subnet B in Availability Zone B (AZ B).
This configuration allowed for secure access to private instances without exposing them directly to the internet. The bastion host effectively controlled and monitored SSH access, enhancing the overall security posture of the architecture.


Security Group:
The bastion host's security group was configured to allow SSH traffic solely from my IP address, which further fortified security by limiting access.


Unfortunately, I mistakenly forgot to take screenshots or detailed documentation of the working bastion host before terminating my instances and deleting my VPC. However, I understand its critical role in the architecture and will ensure to improve my documentation practices in future projects.




🎯 Key Concepts Demonstrated

High availability using multiple Availability Zones

Public vs Private subnet design

Internet Gateway vs NAT Gateway functionality

Application Load Balancer configuration

Target group health checks

Security group referencing (ALB → EC2)

Bastion host SSH pattern

Debugging 504 Gateway Timeout errors



🛠️ Challenges Faced & Solutions
1️⃣ 504 Gateway Timeout

Cause:

Apache was not installed or running on private EC2.

Solution:

Verified service status using systemctl

Installed and started Apache manually

2️⃣ Unhealthy Target Group

Cause:

Incorrect security group configuration blocking ALB traffic.

Solution:

Allowed HTTP (80) from ALB Security Group to Private EC2 Security Group.

3️⃣ SSH Access Issues

Cause:

Attempted direct SSH into private EC2.

Solution:

Used Bastion host and security group referencing.

