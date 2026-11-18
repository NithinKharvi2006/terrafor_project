# Terraform AWS Infrastructure Project

## Overview

This project demonstrates how to provision AWS infrastructure using **Terraform (Infrastructure as Code)**.

It creates a custom AWS Virtual Private Cloud (VPC), two subnets, an Internet Gateway, route tables, security groups, two EC2 web servers, and an Application Load Balancer (ALB).

The EC2 instances use Apache web server, which is installed automatically through Terraform user data scripts.

The Application Load Balancer distributes incoming HTTP traffic between the two EC2 instances.

## Architecture

```text
                      Internet
                          |
                          |
                 Internet Gateway
                          |
                          |
                Application Load Balancer
                          |
                    Target Group
                     (HTTP: 80)
                     /         \
                    /           \
                   /             \
            EC2 Instance 1    EC2 Instance 2
              Web Server        Web Server
             Apache HTTP       Apache HTTP
                  |                 |
            Public Subnet 1    Public Subnet 2
             10.0.0.0/24        10.0.1.0/24
              us-east-1a         us-east-1b
                    \             /
                     \           /
                       Custom VPC
                      10.0.0.0/16
```

## AWS Resources Created

| Resource | Description |
|---|---|
| VPC | Creates a custom Virtual Private Cloud |
| Subnet 1 | Creates the first subnet in Availability Zone us-east-1a |
| Subnet 2 | Creates the second subnet in Availability Zone us-east-1b |
| Internet Gateway | Provides internet connectivity to the VPC |
| Route Table | Configures the default IPv4 route through the Internet Gateway |
| Route Table Associations | Associates both subnets with the route table |
| Security Group | Allows inbound HTTP (80) and SSH (22) traffic |
| EC2 Instance 1 | Launches the first web server |
| EC2 Instance 2 | Launches the second web server |
| Application Load Balancer | Distributes incoming HTTP requests |
| Target Group | Registers both EC2 instances as targets |
| ALB Listener | Listens on port 80 and forwards requests to the target group |

## Technologies Used

- Terraform
- Amazon Web Services (AWS)
- Amazon EC2
- Amazon VPC
- Amazon Application Load Balancer
- AWS Security Groups
- AWS Internet Gateway
- AWS Route Tables
- Linux
- Apache Web Server
- Bash Shell Scripting
- Git and GitHub

## Project Structure

```text
terraform_proj/
│
├── main.tf
│   └── AWS infrastructure resources
│
├── provider.tf
│   └── Terraform and AWS provider configuration
│
├── variables.tf
│   └── Input variable definitions
│
├── userdata.sh
│   └── Initialization script for EC2 instance 1
│
├── userdata1.sh
│   └── Initialization script for EC2 instance 2
│
├── .terraform.lock.hcl
│   └── Provider dependency lock file
│
├── .gitignore
│   └── Files excluded from Git
│
└── README.md
    └── Project documentation
```

## How It Works

### 1. VPC Creation

Terraform creates a custom VPC with the CIDR block:

```text
10.0.0.0/16
```

### 2. Subnet Creation

Two subnets are created in different Availability Zones:

```text
Subnet 1: 10.0.0.0/24
Availability Zone: us-east-1a

Subnet 2: 10.0.1.0/24
Availability Zone: us-east-1b
```

### 3. Internet Connectivity

An Internet Gateway is attached to the VPC.

A route table is configured with the following default route:

```text
Destination: 0.0.0.0/0
Target: Internet Gateway
```

Both subnets are associated with this route table.

### 4. Security Group Configuration

A security group is created with these inbound rules:

| Protocol | Port | Purpose |
|---|---:|---|
| TCP | 80 | HTTP traffic |
| TCP | 22 | SSH access |

Outbound traffic is allowed.

### 5. EC2 Instance Creation

Terraform launches two EC2 instances using the configured Amazon Machine Image (AMI).

Both instances use:

```text
Instance Type: t2.micro
Operating System: Defined by the selected AMI
Web Server: Apache
```

### 6. User Data Configuration

Two Bash scripts are used to configure the EC2 instances automatically.

The scripts:

- Update system packages.
- Install Apache.
- Install AWS CLI.
- Retrieve the EC2 instance ID.
- Create a custom HTML webpage.
- Start and enable the Apache service.

### 7. Application Load Balancer

An internet-facing Application Load Balancer is created.

It includes:

- One target group.
- Two registered EC2 instances.
- One HTTP listener on port 80.
- Health checks to monitor target availability.

Incoming HTTP requests are forwarded to the target group.

## Prerequisites

Before deploying this project, install:

1. Terraform CLI.
2. AWS CLI.
3. Git.
4. An AWS account with the required permissions.

Verify the installations:

```bash
terraform version
aws --version
git --version
```

Configure AWS credentials:

```bash
aws configure
```

The AWS provider is configured to use:

```text
Region: us-east-1
```

Verify your AWS identity:

```bash
aws sts get-caller-identity
```

## Deployment Instructions

### Step 1: Clone the Repository

```bash
git clone https://github.com/NithinKharvi2006/terrafor_project.git
```

### Step 2: Navigate to the Project

```bash
cd terrafor_project
```

### Step 3: Initialize Terraform

```bash
terraform init
```

This downloads the required Terraform provider plugins.

### Step 4: Format Terraform Files

```bash
terraform fmt -recursive
```

### Step 5: Validate the Configuration

```bash
terraform validate
```

This checks whether the Terraform configuration is valid.

### Step 6: Preview Infrastructure Changes

```bash
terraform plan
```

This displays the AWS resources Terraform plans to create, modify, or destroy.

### Step 7: Create AWS Infrastructure

```bash
terraform apply
```

Review the execution plan and confirm when prompted.

### Step 8: Get the Load Balancer DNS

After successful deployment, run:

```bash
terraform output loadbalancerdns
```

Open the returned DNS name in your browser using HTTP:

```text
http://<load-balancer-dns-name>
```

## Cleanup

To remove the infrastructure created by Terraform, run:

```bash
terraform destroy
```

Review the destruction plan and confirm when prompted.

This helps avoid unnecessary AWS charges.

## Security Considerations

- The current security group allows SSH access from `0.0.0.0/0`. Restrict SSH access to trusted IP addresses for better security.
- The current ALB listener uses HTTP. Use HTTPS with an appropriate TLS certificate for production deployments.
- Never upload AWS access keys, passwords, private keys, or sensitive Terraform state files to GitHub.
- Terraform state files are excluded through `.gitignore`.
- Verify IAM permissions before creating AWS resources.
- Review AWS pricing before deploying infrastructure.

## Important Notes

- The AMI configured in the project must exist in the selected AWS region.
- The project uses the AWS region `us-east-1`.
- EC2 instances, EBS volumes, Application Load Balancers, and data transfer may incur AWS charges.
- Check subnet public IP assignment and connectivity settings when deploying the web servers.
- This is a learning project and should be reviewed and hardened before production use.

## Learning Outcomes

Through this project, I learned:

- Infrastructure as Code using Terraform.
- AWS VPC and subnet creation.
- Internet Gateway configuration.
- Route table configuration and associations.
- AWS security group configuration.
- EC2 instance provisioning.
- Automated server configuration using Bash scripts.
- Application Load Balancer configuration.
- Target groups and listeners.
- Terraform initialization, validation, planning, and deployment.
- Git and GitHub integration.

## GitHub Repository

**Project Repository:**

https://github.com/NithinKharvi2006/terrafor_project

---

**Author:** Nithin Kharvi

**Project:** AWS Infrastructure Automation Using Terraform
