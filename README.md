# AWS Static Web Page Deployment with EC2, Auto Scaling Group, Load Balancer, and AMI

## Project Overview
This project demonstrates the deployment of a static website (Modern Musician template) on AWS using a comprehensive infrastructure setup including EC2 instances, Auto Scaling Groups, Application Load Balancer, and custom AMI creation. The infrastructure ensures high availability, scalability, and fault tolerance for the web application.

## Architecture Components

### 1. **EC2 Instances**
- **Primary Instance (aws-web)**
  - Instance ID: `i-07d3332629a055f73`
  - Public IP: `65.0.129.120`
  - Private IP: `172.31.33.220`
  - Instance Type: `t3.micro`
  - Status: Running with 3/3 checks passed
  - Availability Zone: ap-south-1a
<img width="3710" height="1793" alt="Screenshot 2026-03-13 002854" src="https://github.com/user-attachments/assets/fbdd2334-f1c9-4a03-8a7f-05b16488bd5d" />

<img width="3728" height="1804" alt="Screenshot 2026-03-13 001234" src="https://github.com/user-attachments/assets/5b0eb007-6364-4204-8daf-bf530663b74c" />

- **Auto Scaling Instance**
  - Instance ID: `i-0ee5849b1374ce73c`
  - Public IP: `13.203.217.137`
  - Private IPs: `172.31.9.62`, `172.31.9.63`
  - Instance Type: `t3.micro`
  - Status: Running with 3/3 checks passed
  - Availability Zone: ap-south-1b
<img width="3708" height="1767" alt="Screenshot 2026-03-13 001131" src="https://github.com/user-attachments/assets/0a76c0a7-74d5-4b24-ad9a-2b67424fa924" />

- **Terminated Instance**
  - Instance ID: `i-0db9692330eab1972` (static web)
  - Instance Type: `t3.micro`
  - Availability Zone: ap-south-1a

### 2. **Web Application Deployment**
The static website "Modern Musician" was deployed with the following steps:

```bash
# Download and extract the web application
wget [application-zip-url] -O app.zip
unzip app.zip

# Copy files to web server directory
sudo cp -r "2155 modern musician"/* /var/www/html/
```

The website includes:
- HTML/CSS/JavaScript files
- Musician avatar images
- Audio sample functionality
- Responsive design template
<img width="3839" height="2023" alt="Screenshot 2026-03-12 234922" src="https://github.com/user-attachments/assets/fbc33fc5-59c9-4c09-a1ac-4d3bf1ba5a35" />

### 3. **Auto Scaling Group Configuration**
- **Group Name**: `web auto scale`
- **Launch Template**: `web-temp` (Version: Default)
- **Capacity Settings**:
  - Desired Capacity: 1 instance
  - Minimum Capacity: 1 instance
  - Maximum Capacity: 4 instances
- **Availability Zones**: 3 zones available

**Scaling Activity**:
- Successfully launched instance `i-0ee5849b1374ce73c`
- Trigger: User request changed desired capacity from 0 to 1
- Execution time: March 13, 2026 at 12:05:17 AM IST

- <img width="3712" height="1780" alt="Screenshot 2026-03-13 002138" src="https://github.com/user-attachments/assets/51d73a46-09f3-458f-b1b6-070859409c2d" />


### 4. **Load Balancer**
- **Name**: `app-kish-load`
- **Type**: Application Load Balancer (Internet-facing)
- **Scheme**: Internet-facing
- **IP Address Type**: IPv4
- **VPC**: vpc-0e
- **Status**: Provisioning

<img width="3705" height="1783" alt="Screenshot 2026-03-13 000252" src="https://github.com/user-attachments/assets/7ef1332e-2315-46e8-bc4f-9799191daf1e" />


### 5. **Target Group**
- **Protocol**: HTTP
- **Target Type**: Instance
- Status: Configured for load balancing
<img width="3710" height="1776" alt="Screenshot 2026-03-13 000303" src="https://github.com/user-attachments/assets/a77f8871-0e19-4b9f-87f8-7dbed2be8bca" />

### 6. **AMI and Snapshots**
- **AMI ID**: `ami-0d41729fd809548c7`
  - Created from instance `i-07d3332629a055f73`
  - Status: Being created (pending availability)
  
- **Snapshot**: `snap-0acc531adcc08eea6`
  - Size: 8 GiB
  - Source Volume: 8 GiB
  - Description: "Created by CreateImage(i-...)"
  - Purpose: Backup and AMI creation
![Uploading Screenshot 2026-03-12 235523.png…]()

### 7. **Launch Template**
- **Template ID**: `lt-08253e718cbf4820a`
- **Template Name**: `web-term`
- **Default Version**: Active
- **Purpose**: Defines instance configuration for Auto Scaling

### 8. **Performance Testing**

**Stress Testing on Auto Scaling Instance**:
```bash

# Install stress testing tool
apt-get install stress

# Execute CPU stress tests
stress --cpu 2 --timeout 250  # 250 seconds of CPU stress
stress --cpu 2 --timeout 400  # 400 seconds of CPU stress
```

**Performance Metrics** (from 18:00 to 18:30):
- **CPU Utilization**: Peaked at 100%
- **Network In**: Up to 42.77M bytes
- **Network Out**: Up to 222.1k bytes
- **Network Packets In**: Up to 29.03K count

<img width="3706" height="1807" alt="Screenshot 2026-03-13 001145" src="https://github.com/user-attachments/assets/b38dfbfc-0ac4-46d6-abb9-8dcf589622cc" />

<img width="3089" height="733" alt="Screenshot 2026-03-13 002949" src="https://github.com/user-attachments/assets/df599342-71d0-4ef1-9292-7c37d67c2ee9" />

## Deployment Workflow

1. **Initial Setup**
   - Launched base EC2 instance (aws-web)
   - Deployed Modern Musician static website
   - Verified application accessibility

2. **AMI Creation**
   - Created AMI from running instance
   - Generated EBS snapshot for backup

3. **Auto Scaling Configuration**
   - Created launch template from AMI
   - Configured Auto Scaling Group with desired capacities
   - Set up scaling policies

4. **Load Balancer Setup**
   - Created Application Load Balancer
   - Configured target group
   - Registered instances with load balancer

5. **Testing**
   - Performed stress testing on new instances
   - Monitored performance metrics
   - Verified auto-scaling capabilities

## Security Groups
Security groups were configured to allow:
- HTTP (port 80) traffic from internet
- SSH (port 22) for management
- Load balancer to instance communication

## Monitoring and Metrics
- **CloudWatch Metrics**: CPU utilization, network I/O
- **Status Checks**: All instances passing 3/3 checks
- **Auto Scaling Activities**: Tracked in activity history
- **Load Balancer Metrics**: Monitoring target health

## Key Learnings and Best Practices Implemented

1. **High Availability**: Multi-AZ deployment
2. **Scalability**: Auto Scaling Group with configurable limits
3. **Fault Tolerance**: Load balancer distributes traffic
4. **Backup Strategy**: AMI and snapshot creation
5. **Performance Testing**: Stress testing for reliability
6. **Infrastructure as Code**: Launch templates for consistency

## Infrastructure Management Commands

```bash
# View instances
aws ec2 describe-instances --instance-ids i-07d3332629a055f73

# Create AMI
aws ec2 create-image --instance-id i-07d3332629a055f73 --name "web-ami" --no-reboot

# Update Auto Scaling Group
aws autoscaling update-auto-scaling-group --auto-scaling-group-name "web auto scale" --min-size 1 --max-size 4 --desired-capacity 2
77
# Check load balancer status
aws elbv2 describe-load-balancers --names app-kish-load
```

## Conclusion
This project successfully demonstrates a production-ready AWS infrastructure for hosting a static website. The combination of EC2, Auto Scaling, Load Balancing, and AMI backup provides a robust, scalable, and highly available solution suitable for various web applications. The architecture ensures minimal downtime, automatic scaling during traffic spikes, and easy disaster recovery through AMI backups.
