
---

# Hosting a Static Website 

This project demonstrates how to host a static HTML website on AWS using best DevOps practices and core AWS infrastructure services. The site is deployed on EC2 instances within a secure, highly available, and scalable environment. The infrastructure was deployed manually (or optionally via script/CloudFormation/Terraform in extended versions) and the web files are version-controlled via GitHub.

## 📌 Project Overview

The goal of this project is to deploy a secure and fault-tolerant static web application on AWS by leveraging multiple services for high availability, scalability, and security.

## 🔧 Architecture Summary

Below is an overview of the key components configured for the project:

1. **VPC Configuration**

   * Created a custom VPC with **public and private subnets** across **two Availability Zones** for high availability and fault tolerance.

2. **Networking & Security**

   * **Internet Gateway** for outbound internet access from public subnet.
   * **NAT Gateway** in public subnet to enable outbound access from private subnet EC2 instances.
   * **EC2 Instance Connect Endpoint** for secure connection to EC2s in private subnets.
   * **Security Groups** configured to allow appropriate traffic to web servers and load balancer.

3. **Compute Infrastructure**

   * **EC2 Instances** hosted in **private subnets** to serve the static website content.
   * **Auto Scaling Group (ASG)** manages the number of EC2 instances based on traffic/load.
   * **Application Load Balancer (ALB)** evenly distributes incoming traffic across instances in multiple Availability Zones.

4. **Web Application Deployment**

   * Web content is cloned from a GitHub repository to EC2 instances during provisioning.
   * Apache Web Server is used to serve the static site.

5. **Monitoring and Alerts**

   * **Amazon SNS** (Simple Notification Service) sends alerts related to ASG activities.

6. **Domain and SSL**

   * **Route 53** manages DNS and domain registration.
   * **AWS Certificate Manager (ACM)** provides SSL certificates for secure HTTPS communication.

## 📁 Project Repository Structure

The GitHub repository contains the following:


* Bash Script to bootstrap EC2 instances
* HTML/CSS files for the website

## 🚀 Deployment Bash Script

Here's the script used to install Apache, clone the repository, and serve the static content:

```bash
#!/bin/bash

# Switch to the root user to gain full administrative privileges
sudo su

# Update all installed packages to their latest versions
yum update -y

# Install Apache HTTP Server
yum install -y httpd

# Change the current working directory to the Apache web root
cd /var/www/html

# Install Git
yum install git -y

# Clone the project GitHub repository to the current directory
git clone https://github.com/cloudjingo/Hosting-a-static-web-site.git

# Copy all files, including hidden ones, from the cloned repository to the Apache web root
cp -R Hosting-a-static-web-site/. /var/www/html/

# Remove the cloned repository directory to clean up unnecessary files
rm -rf Hosting-a-static-web-site

# Enable the Apache HTTP Server to start automatically at system boot
systemctl enable httpd 

# Start the Apache HTTP Server to serve web content
systemctl start httpd
```

## 🌐 Domain and DNS Configuration

* **Route 53** is used to register a custom domain and create DNS records.
* **ACM** SSL certificate is provisioned and attached to the ALB for HTTPS support.

## 📦 Tools & Technologies

* AWS (VPC, EC2, ALB, ASG, Route 53, ACM, SNS)
* Apache HTTP Server
* Git & GitHub
* Bash
* HTML/CSS

## 📈 Benefits

* **High Availability** – Deployed across multiple Availability Zones.
* **Scalability** – Auto Scaling Group adjusts EC2 instances based on demand.
* **Security** – Private subnet deployment with NAT Gateway, security groups, and encrypted traffic.
* **Version Control** – Web content is pulled directly from GitHub.

## 🧹 Cleanup

To avoid unexpected charges, delete the following resources in this order:

Auto Scaling Group (and associated Launch Configurations)

Application Load Balancer (Listeners, Target Groups)

EC2 Instances (if any remain)

NAT Gateway and Elastic IP

Internet Gateway

VPC and Subnets

ACM Certificate (optional)

Route 53 Hosted Zone (optional)

SNS Topic

