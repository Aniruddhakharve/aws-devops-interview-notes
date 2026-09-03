# AWS EC2 Fundamentals — Interview Notes

> **Course:** Section 5 — EC2 Fundamentals  
> **Scope:** Lectures 32–47. Advanced EC2 topics outside this section are excluded.

---

## 1. AWS Budget

**AWS Budgets** helps monitor AWS spending and sends alerts when actual or forecasted costs reach configured thresholds.

```text
AWS Account
   ↓
Create Budget
   ↓
Set threshold
   ↓
Configure alert
   ↓
Receive notification
```

**Remember:** A budget is primarily for monitoring/alerts; reaching a budget threshold does not automatically stop all AWS resources.

---

## 2. What is Amazon EC2?

**Amazon EC2 (Elastic Compute Cloud)** provides resizable compute capacity in AWS.

In simple terms:

> **EC2 is a virtual server in the AWS Cloud.**

When launching EC2, you commonly choose:

- AMI
- Instance type
- Key pair
- VPC/subnet
- Security Group
- Storage
- IAM role, if required
- Purchasing option

```text
EC2
├── AMI              → OS/software template
├── Instance Type    → CPU/memory/network capacity
├── EBS              → Block storage
├── Security Group   → Network firewall
├── Key Pair         → SSH authentication
├── User Data        → Startup configuration
└── IAM Role         → AWS permissions
```

---

## 3. EC2 User Data

**User Data** lets you run commands/scripts during instance initialization.

Example:

```bash
#!/bin/bash
dnf install -y httpd
systemctl enable --now httpd
echo "Hello from EC2" > /var/www/html/index.html
```

Typical flow:

```text
Launch EC2
   ↓
User Data runs
   ↓
Install/configure software
   ↓
Start application
```

**Use case:** Bootstrapping a web server or installing initial dependencies.

---

## 4. EC2 Instance Types

An instance type determines the compute resources available to an EC2 instance.

| Family | Typical workload |
|---|---|
| General Purpose | Balanced CPU and memory |
| Compute Optimized | CPU-intensive workloads |
| Memory Optimized | Memory-intensive workloads |
| Storage Optimized | High storage performance |
| Accelerated Computing | GPU/specialized workloads |

Easy memory trick:

```text
General Purpose → Balanced
Compute         → CPU
Memory          → RAM
Storage         → Storage
Accelerated     → GPU/specialized
```

---

## 5. Security Groups

A **Security Group (SG)** acts as a virtual firewall for EC2.

It controls:

- Inbound traffic
- Outbound traffic
- Protocols
- Ports
- Sources/destinations

### Important characteristics

- Security Groups are **stateful**.
- Inbound traffic is denied unless allowed by an inbound rule.
- Security Groups support **allow rules**, not explicit deny rules.

Example:

```text
SSH:
TCP 22 → Your IP

HTTP:
TCP 80 → Internet
```

For SSH, prefer allowing your own IP rather than:

```text
0.0.0.0/0 → TCP 22
```

when possible.

---

## 6. Common Ports

| Port | Common use |
|---:|---|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 21 | FTP |
| 25 | SMTP |
| 53 | DNS |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 8080 | Common application/dev port |

---

## 7. SSH

**SSH (Secure Shell)** is used to securely connect to a remote Linux server.

Typical flow:

```text
Your Computer
    ↓
Network
    ↓
Security Group
    ↓
EC2
    ↓
SSH
```

Example:

```bash
ssh -i my-key.pem ec2-user@PUBLIC_IP
```

The username depends on the AMI. For example:

```text
Amazon Linux → ec2-user
Ubuntu       → ubuntu
```

SSH commonly requires:

- Instance is running
- Correct network path/IP
- Security Group allows TCP 22
- Correct private key
- Correct username
- SSH service is available

---

## 8. SSH Troubleshooting

Use a systematic approach:

```text
1. Is EC2 running?
        ↓
2. Correct IP/DNS?
        ↓
3. Network path available?
        ↓
4. Security Group allows TCP 22?
        ↓
5. Correct private key?
        ↓
6. Correct username?
        ↓
7. SSH service/configuration okay?
```

For Linux/macOS key permissions:

```bash
chmod 400 my-key.pem
```

**Interview tip:** Don't immediately assume "SSH is broken." Work from network reachability → port → authentication → OS/service.

---

## 9. EC2 Instance Connect

**EC2 Instance Connect** provides an AWS-supported way to connect to supported EC2 instances, including through the AWS Console.

It can simplify SSH access by using temporary SSH credentials rather than requiring you to manually manage a private key for every connection.

```text
AWS Console
    ↓
EC2 Instance Connect
    ↓
EC2
```

---

## 10. IAM Roles for EC2

If an application running on EC2 needs to access AWS services such as S3 or CloudWatch, use an **IAM Role** instead of storing long-lived AWS access keys on the server.

```text
EC2
 ↓
IAM Instance Profile
 ↓
IAM Role
 ↓
Permissions Policy
 ↓
AWS Service
```

The workload can obtain **temporary credentials** for the role.

Example:

```text
EC2 Application
      ↓
EC2 IAM Role
      ↓
s3:GetObject
      ↓
S3 Bucket
```

### Interview answer

> I would create an IAM role with only the required permissions, configure EC2 as the trusted entity, and attach the role to the instance through an IAM instance profile. This avoids storing long-lived AWS access keys on the server.

---

## 11. EC2 Purchasing Options

### On-Demand

Pay for capacity without a long-term commitment.

Good for:

- Short-term workloads
- Unpredictable workloads
- Development/testing

### Reserved Instances

Commit to a term for discounted pricing compared with On-Demand.

Good for:

- Predictable, long-running workloads

### Savings Plans

Commit to a certain level of compute spend over a term for discounted rates.

### Spot Instances

Use spare AWS capacity at potentially large discounts.

Good for:

- Fault-tolerant workloads
- Batch processing
- Flexible workloads
- Stateless workloads

Trade-off:

```text
Lower cost
   +
Possible interruption
```

---

## 12. Spot Instances & Spot Fleet

### Spot Instance

A Spot Instance uses spare EC2 capacity at a discounted price.

AWS can interrupt Spot capacity, so applications must tolerate interruptions.

### Spot Fleet

Spot Fleet is used to request and manage a collection of Spot capacity according to defined requirements.

Simple idea:

```text
Workload requirement
       ↓
Spot Fleet
       ↓
Available Spot capacity
```

**Interview point:** Spot is primarily a cost-optimization choice for workloads that can tolerate interruption.

---

## 13. EC2 Launch Checklist

When launching an EC2 instance, think:

```text
AMI
 ↓
Instance Type
 ↓
Key Pair
 ↓
VPC / Subnet
 ↓
Security Group
 ↓
Storage
 ↓
IAM Instance Profile
 ↓
User Data
 ↓
Launch
```

---

## 14. Common Confusions

### Security Group vs IAM

```text
Security Group
→ Controls network traffic

IAM
→ Controls permissions to AWS resources
```

Example:

```text
SG → Can traffic reach port 22?

IAM → Can this EC2 workload call S3?
```

These are different security layers.

### User Data vs IAM Role

```text
User Data
→ What should run during startup?

IAM Role
→ What AWS resources can the workload access?
```

### SSH vs EC2 Instance Connect

```text
SSH
→ Secure remote access protocol

EC2 Instance Connect
→ AWS-supported connection mechanism for supported EC2 instances
```

### Public IP vs IAM

```text
Public IP
→ Network reachability

IAM
→ Authorization
```

A public IP does **not** give an EC2 instance permission to access S3.

---

# 15. How to Explain EC2 in an Interview

### Q: What is EC2?

> Amazon EC2 is an AWS service that provides resizable virtual compute capacity. When launching an instance, we choose an AMI, instance type, networking, storage, security controls, and optionally an IAM role. EC2 can be used to run web servers, APIs, applications, and other workloads.

### Q: How would you securely give EC2 access to S3?

> I would attach an IAM role to the EC2 instance with only the required S3 permissions. The role provides temporary credentials to the workload, so I don't need to store long-lived AWS access keys on the server.

---

# 16. Interview Questions

## Basic

1. What is Amazon EC2?
2. What is an AMI?
3. What is an EC2 instance type?
4. What is a Security Group?
5. What is EC2 User Data?
6. What is SSH?
7. What is EC2 Instance Connect?

## Intermediate

8. What happens when you launch an EC2 instance?
9. What is the difference between a Security Group and an IAM Role?
10. Why do we use IAM Roles with EC2?
11. What is the purpose of User Data?
12. What is the difference between On-Demand and Spot Instances?
13. Why are Spot Instances cheaper?
14. What happens if a Spot Instance is interrupted?
15. What is the difference between Reserved Instances and Savings Plans?

## Scenario-Based

### Q16. Your EC2 website is not opening in a browser. What would you check?

> I would verify that the instance is running, check network reachability, verify the Security Group allows the required application port such as 80 or 443, check that the web server is running and listening on the expected port, and then investigate subnet routes or other network controls if necessary.

### Q17. SSH to EC2 is failing. What would you check?

> I would check the instance state, IP/DNS, network path, Security Group port 22 rule, key pair/private key, username, and finally the SSH service and instance configuration.

### Q18. EC2 needs to download files from S3 without storing AWS credentials. What would you do?

> I would create an IAM role with only the required S3 permissions and attach it to EC2 through an IAM instance profile. The application can then use temporary credentials.

### Q19. A batch workload can tolerate interruption and cost needs to be minimized. Which EC2 option would you consider?

> I would consider Spot Instances because they can provide significant discounts, provided the workload is designed to tolerate interruption.

---

# 17. Hands-On Scenarios to Practice

### Scenario 1 — Launch a Web Server

Launch EC2 and use User Data to:

```text
Install web server
      ↓
Start web server
      ↓
Create index.html
      ↓
Open website
```

Verify that the Security Group allows HTTP.

### Scenario 2 — Secure SSH

Configure:

```text
TCP 22
Source → Your IP
```

Test SSH access and explain why restricting the source is safer.

### Scenario 3 — EC2 → S3

Create:

```text
IAM Role
   ↓
S3 read permission
   ↓
Attach role to EC2
   ↓
aws s3 ls
```

Test access without manually storing long-lived AWS credentials on EC2.

### Scenario 4 — SSH Troubleshooting

Practice explaining this flow aloud:

```text
Instance
 ↓
Network reachability
 ↓
Security Group
 ↓
Port 22
 ↓
Key
 ↓
Username
 ↓
SSH service
```

### Scenario 5 — Choose a Purchasing Option

Decide which option fits:

```text
1. Temporary development server
2. Predictable long-running workload
3. Fault-tolerant batch processing
4. Flexible workload that can tolerate interruption
```

Explain **why** for each answer.

---

# 18. Quick Revision Cheat Sheet

```text
EC2
→ Virtual compute capacity

AMI
→ Template used to launch an instance

INSTANCE TYPE
→ CPU / memory / networking capacity

USER DATA
→ Startup/bootstrap commands

SECURITY GROUP
→ Stateful virtual firewall

SSH
→ Secure remote shell

22
→ SSH

80
→ HTTP

443
→ HTTPS

IAM ROLE
→ AWS permissions for the EC2 workload

INSTANCE PROFILE
→ Associates an IAM role with EC2

EC2 INSTANCE CONNECT
→ AWS-supported connection method

ON-DEMAND
→ Flexible, no long-term commitment

RESERVED / SAVINGS PLANS
→ Savings through commitment

SPOT
→ Discounted spare capacity; can be interrupted

SPOT FLEET
→ Request/manage Spot capacity

AWS BUDGET
→ Spending monitoring and alerts
```

---

# ⭐ 30-Second EC2 Interview Summary

> **EC2 provides virtual compute capacity in AWS. When launching an instance, we select an AMI, instance type, networking, storage, Security Group, key pair, and optionally an IAM role and User Data. Security Groups control network traffic, while IAM roles provide AWS permissions to the workload. User Data can bootstrap the instance at startup. For cost optimization, AWS provides options such as On-Demand, Reserved Instances, Savings Plans, and Spot depending on workload requirements and interruption tolerance.**

### Golden distinction

```text
Security Group → Network access
IAM Role       → AWS permissions
User Data      → Startup configuration
Key Pair       → SSH authentication
Instance Type  → Compute capacity
AMI            → OS/software template
```
