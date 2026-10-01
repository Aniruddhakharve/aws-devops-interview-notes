# AWS — High Availability and Scalability: ELB & ASG — Interview Notes

> **Course:** Section 8 — High Availability and Scalability: ELB & ASG  
> **Lectures:** 70–86  
> **Scope:** High Availability, Scalability, Elastic Load Balancing, ALB, NLB, GWLB, Sticky Sessions, Cross-Zone Load Balancing, SSL/TLS, Connection Draining, Auto Scaling Groups, and Scaling Policies  
> **Goal:** Easy understanding + hands-on revision + interview preparation

---

## 📑 Table of Contents

- [1. High Availability and Scalability](#1-high-availability-and-scalability)
- [2. Vertical vs Horizontal Scaling](#2-vertical-vs-horizontal-scaling)
- [3. What is Elastic Load Balancing?](#3-what-is-elastic-load-balancing)
- [4. ELB Health Checks](#4-elb-health-checks)
- [5. Types of Elastic Load Balancers](#5-types-of-elastic-load-balancers)
- [6. ALB — Application Load Balancer](#6-alb--application-load-balancer)
- [7. ALB Target Groups](#7-alb-target-groups)
- [8. ALB Routing](#8-alb-routing)
- [9. ALB Listener and Listener Rules](#9-alb-listener-and-listener-rules)
- [10. ALB Hands-On Architecture](#10-alb-hands-on-architecture)
- [11. ELB Security Groups](#11-elb-security-groups)
- [12. Why Use a Target Group?](#12-why-use-a-target-group)
- [13. NLB — Network Load Balancer](#13-nlb--network-load-balancer)
- [14. NLB Static IP Concept](#14-nlb-static-ip-concept)
- [15. NLB Target Groups](#15-nlb-target-groups)
- [16. NLB Health Checks](#16-nlb-health-checks)
- [17. NLB Hands-On Lesson](#17-nlb-hands-on-lesson)
- [18. ALB vs NLB](#18-alb-vs-nlb)
- [19. GWLB — Gateway Load Balancer](#19-gwlb--gateway-load-balancer)
- [20. GWLB Architecture](#20-gwlb-architecture)
- [21. GWLB Key Concepts](#21-gwlb-key-concepts)
- [22. Sticky Sessions](#22-sticky-sessions)
- [23. Why Use Sticky Sessions?](#23-why-use-sticky-sessions)
- [24. Disadvantage of Sticky Sessions](#24-disadvantage-of-sticky-sessions)
- [25. Sticky Session Cookies](#25-sticky-session-cookies)
- [26. Cross-Zone Load Balancing](#26-cross-zone-load-balancing)
- [27. Cross-Zone Example](#27-cross-zone-example)
- [28. Important Cross-Zone Exam Concept](#28-important-cross-zone-exam-concept)
- [29. SSL/TLS Certificates](#29-ssltls-certificates)
- [30. AWS Certificate Manager — ACM](#30-aws-certificate-manager--acm)
- [31. SSL/TLS Listener](#31-ssltls-listener)
- [32. SNI — Server Name Indication](#32-sni--server-name-indication)
- [33. SNI Simple Flow](#33-sni-simple-flow)
- [34. Connection Draining / Deregistration Delay](#34-connection-draining--deregistration-delay)
- [35. Why Deregistration Delay Matters](#35-why-deregistration-delay-matters)
- [36. Auto Scaling Group — ASG](#36-auto-scaling-group--asg)
- [37. ASG Minimum, Desired and Maximum Capacity](#37-asg-minimum-desired-and-maximum-capacity)
- [38. ASG and Load Balancer](#38-asg-and-load-balancer)
- [39. ASG Can Replace Unhealthy Instances](#39-asg-can-replace-unhealthy-instances)
- [40. Launch Template](#40-launch-template)
- [41. ASG Architecture](#41-asg-architecture)
- [42. ASG and Multi-AZ](#42-asg-and-multi-az)
- [43. ASG Hands-On Flow](#43-asg-hands-on-flow)
- [44. ASG Hands-On — Scaling Out](#44-asg-hands-on--scaling-out)
- [45. ASG Hands-On — Scaling In](#45-asg-hands-on--scaling-in)
- [46. ASG Scaling Policies](#46-asg-scaling-policies)
- [47. Target Tracking Scaling](#47-target-tracking-scaling)
- [48. Target Tracking Example](#48-target-tracking-example)
- [49. Step Scaling](#49-step-scaling)
- [50. Simple Scaling](#50-simple-scaling)
- [51. Scheduled Scaling](#51-scheduled-scaling)
- [52. Predictive Scaling](#52-predictive-scaling)
- [53. Common Scaling Metrics](#53-common-scaling-metrics)
- [54. Scaling Cooldown](#54-scaling-cooldown)
- [55. Ready-to-Use AMIs and Scaling](#55-ready-to-use-amis-and-scaling)
- [56. Detailed Monitoring and Scaling](#56-detailed-monitoring-and-scaling)
- [57. ALB vs NLB vs GWLB — Most Important Table](#57-alb-vs-nlb-vs-gwlb--most-important-table)
- [58. ELB vs ASG](#58-elb-vs-asg)
- [59. ELB Does Not Automatically Create EC2 Instances](#59-elb-does-not-automatically-create-ec2-instances)
- [60. ASG Does Not Replace the Load Balancer](#60-asg-does-not-replace-the-load-balancer)
- [61. Most Important Architecture to Remember](#61-most-important-architecture-to-remember)
- [62. Common Confusions](#62-common-confusions)
- [63. How to Explain High Availability and Scalability in an Interview](#63-how-to-explain-high-availability-and-scalability-in-an-interview)
- [64. How to Explain ELB in an Interview](#64-how-to-explain-elb-in-an-interview)
- [65. How to Explain ALB in an Interview](#65-how-to-explain-alb-in-an-interview)
- [66. How to Explain NLB in an Interview](#66-how-to-explain-nlb-in-an-interview)
- [67. How to Explain GWLB in an Interview](#67-how-to-explain-gwlb-in-an-interview)
- [68. How to Explain Sticky Sessions](#68-how-to-explain-sticky-sessions)
- [69. How to Explain Cross-Zone Load Balancing](#69-how-to-explain-cross-zone-load-balancing)
- [70. How to Explain TLS Termination](#70-how-to-explain-tls-termination)
- [71. How to Explain SNI](#71-how-to-explain-sni)
- [72. How to Explain ASG](#72-how-to-explain-asg)
- [73. How to Explain Target Tracking](#73-how-to-explain-target-tracking)
- [74. Scenario-Based Interview Questions](#74-scenario-based-interview-questions)
- [75. Troubleshooting Load Balancer Targets](#75-troubleshooting-load-balancer-targets)
- [76. Troubleshooting ASG](#76-troubleshooting-asg)
- [77. Hands-On Scenarios to Practice](#77-hands-on-scenarios-to-practice)
- [78. Quick Revision Cheat Sheet](#78-quick-revision-cheat-sheet)
- [⭐ 30-Second Section Summary](#-30-second-section-summary)
- [🧠 Golden Memory Map](#-golden-memory-map)
- [⭐ Most Important Interview Points](#-most-important-interview-points)

---

# 1. High Availability and Scalability

These two concepts are related, but they are **not the same**.

## Scalability

Scalability means:

> **The application can handle an increasing or decreasing workload by adapting its resources.**

There are two main forms:

```text
Scalability
├── Vertical Scaling
└── Horizontal Scaling
```

## Vertical Scaling

Increase the size of an existing instance.

```text
t2.micro
   ↓
t2.large
```

Think:

> **Scale Up / Scale Down**

Example:

A database needs more CPU and RAM → increase the instance size.

Vertical scaling is common for workloads that are not easily distributed, such as some database workloads.

### Limitation

There is a hardware limit to how large one instance can become.

---

## Horizontal Scaling

Increase or decrease the **number of instances**.

```text
1 EC2
   ↓
2 EC2
   ↓
4 EC2
```

Think:

> **Scale Out / Scale In**

This is common for distributed web applications.

```text
Client
   ↓
Load Balancer
   ↓
EC2
EC2
EC2
```

---

## High Availability

High Availability (HA) means designing the application so it can continue operating when part of the infrastructure fails.

In AWS, this commonly means running the application across **multiple Availability Zones**.

```text
Region
│
├── AZ-A → EC2
│
└── AZ-B → EC2
```

If AZ-A has a problem:

```text
AZ-A ❌
AZ-B ✅
```

The application can continue serving traffic from AZ-B.

### Important distinction

```text
Scalability
→ Handle changing workload

High Availability
→ Survive infrastructure failure
```

They often work together, but they solve different problems.

---

# 2. Vertical vs Horizontal Scaling

| Concept | Meaning | AWS terminology |
|---|---|---|
| Vertical scaling | Increase/decrease instance size | Scale up/down |
| Horizontal scaling | Increase/decrease instance count | Scale out/in |
| High availability | Continue operating during failures | Multi-AZ design |

### Memory trick

```text
Vertical
→ Bigger machine

Horizontal
→ More machines

High Availability
→ Survive failure
```

---

# 3. What is Elastic Load Balancing?

**Elastic Load Balancing (ELB)** distributes incoming traffic across multiple targets.

```text
Users
  ↓
ELB
  ↓
EC2 ─┐
EC2 ─┼── Backend
EC2 ─┘
```

The load balancer provides a **single point of contact** for clients while distributing traffic to healthy targets. AWS manages the load balancer infrastructure and scales its capacity as traffic changes.

## Main benefits

- Distribute traffic across multiple targets
- Improve availability
- Health checking
- Avoid sending traffic to unhealthy targets
- Support SSL/TLS termination
- Support multi-AZ architectures
- Provide a single endpoint for clients
- Integrate with services such as Auto Scaling

---

# 4. ELB Health Checks

A load balancer needs to know whether a target is healthy.

For example:

```text
Protocol → HTTP
Port     → 80
Path     → /health
```

The load balancer checks the target periodically.

```text
Healthy
   ↓
Receive traffic

Unhealthy
   ↓
Stop receiving traffic
```

Health checks are configured at the **target group** level for ALB/NLB/GWLB target groups, and ELB routes traffic only to healthy registered targets.

---

# 5. Types of Elastic Load Balancers

The course introduced four types:

```text
ELB
├── Classic Load Balancer (CLB)
├── Application Load Balancer (ALB)
├── Network Load Balancer (NLB)
└── Gateway Load Balancer (GWLB)
```

For modern architectures, the important ones to understand are:

```text
ALB
→ HTTP/HTTPS
→ Layer 7

NLB
→ TCP/UDP/TLS
→ Layer 4

GWLB
→ Network appliances
→ Layer 3
```

---

# 6. ALB — Application Load Balancer

**Application Load Balancer (ALB)** operates at **Layer 7**.

It is designed for application-level traffic such as:

```text
HTTP
HTTPS
```

It is especially useful for:

- Web applications
- Microservices
- Container-based applications
- Content-based routing

---

# 7. ALB Target Groups

A **Target Group** contains the targets that the ALB sends traffic to.

For example:

```text
ALB
 │
 ├── Target Group 1 → User Service
 │
 └── Target Group 2 → Search Service
```

Targets can include supported resources such as:

- EC2 instances
- IP addresses
- Lambda functions

The important mental model is:

```text
Client
   ↓
ALB
   ↓
Listener
   ↓
Listener Rule
   ↓
Target Group
   ↓
Target
```

---

# 8. ALB Routing

One of the biggest advantages of ALB is **content-based routing**.

Routing can be based on things such as:

- URL path
- Host header
- Query string
- HTTP headers
- HTTP request method
- Source IP

## Path-Based Routing

Example:

```text
example.com/users
        ↓
User Target Group

example.com/search
        ↓
Search Target Group
```

This makes ALB very useful for microservices.

---

# 9. ALB Listener and Listener Rules

A **listener** accepts incoming connections on a specific protocol and port.

Example:

```text
HTTP : 80
HTTPS : 443
```

The listener then evaluates its rules.

```text
Request
   ↓
Listener
   ↓
Condition
   ↓
Action
```

Example:

```text
Path = /error
   ↓
Return fixed response
   ↓
404
```

Another example:

```text
Path = /users
   ↓
Forward to User Target Group
```

### Rule priority

Listener rules have priorities.

```text
Priority 1
   ↓
Highest

Priority 5
   ↓
Lower
```

When multiple rules could match, the higher-priority matching rule is evaluated first.

---

# 10. ALB Hands-On Architecture

The course hands-on used:

```text
Internet
   ↓
Application Load Balancer
   ↓
Target Group
   ├── EC2 Instance 1
   └── EC2 Instance 2
```

The ALB provided a DNS name.

Users accessed:

```text
ALB DNS Name
```

instead of directly accessing:

```text
EC2-1
EC2-2
```

Refreshing the ALB endpoint demonstrated that requests could reach different healthy EC2 instances.

When one target was stopped, it became unhealthy and the ALB stopped sending traffic to it.

This demonstrates the value of ELB health checks.

---

# 11. ELB Security Groups

A common secure architecture is:

```text
Internet
    ↓
ALB Security Group
    ↓
EC2 Security Group
```

## ALB Security Group

For a public website:

```text
HTTP 80
HTTPS 443
Source → Internet
```

## EC2 Security Group

Instead of allowing:

```text
0.0.0.0/0 → HTTP
```

allow traffic from the **ALB Security Group**.

```text
EC2 Security Group
        ↑
        │
ALB Security Group
```

This means:

> EC2 accepts application traffic only from the load balancer rather than directly from arbitrary clients.

### Interview memory

```text
Internet → ALB SG
ALB      → EC2 SG
```

---

# 12. Why Use a Target Group?

A target group lets the load balancer manage a collection of backend targets.

```text
ALB
 ↓
Target Group
 ↓
EC2
EC2
EC2
```

It also provides a place to define health checks.

With Auto Scaling, instances launched by the Auto Scaling Group can automatically be registered with the target group.

---

# 13. NLB — Network Load Balancer

**Network Load Balancer (NLB)** operates at **Layer 4**.

Think:

```text
Layer 4
→ TCP
→ UDP
→ TLS
```

NLB is designed for:

- Very high performance
- Very low latency
- TCP/UDP applications
- Applications requiring static IP addresses

---

# 14. NLB Static IP Concept

A key NLB feature is its support for a **fixed IP address per enabled Availability Zone**.

You can also use an Elastic IP for an NLB node in an AZ.

This is useful when:

> The client requires a known set of static IP addresses.

### Exam clue

```text
TCP/UDP
+
Very high performance
+
Static IP requirement
        ↓
NLB
```

---

# 15. NLB Target Groups

NLB can use target groups containing:

- EC2 instances
- Private IP addresses

Example:

```text
NLB
 ↓
Target Group
 ├── EC2
 ├── EC2
 └── EC2
```

---

# 16. NLB Health Checks

NLB target groups can use health checks such as:

```text
TCP
HTTP
HTTPS
```

Example:

```text
NLB
 ↓
Target Group
 ↓
HTTP Health Check
 ↓
EC2
```

If the target fails the health check:

```text
Unhealthy
   ↓
NLB stops routing traffic
```

---

# 17. NLB Hands-On Lesson

The course demonstrated an important troubleshooting scenario.

Initially:

```text
NLB
 ↓
Target Group
 ↓
EC2
```

The targets were unhealthy.

Why?

The EC2 Security Group allowed traffic from the ALB Security Group but did not allow traffic from the NLB path used in the exercise.

After adding the appropriate rule:

```text
NLB
 ↓
EC2 Security Group
 ↓
HTTP allowed
```

the targets became healthy.

### Interview lesson

When an ELB target is unhealthy, investigate:

```text
Health check protocol
Health check port
Health check path
Security Groups
Application/service status
Listener configuration
Target registration
```

---

# 18. ALB vs NLB

| Feature | ALB | NLB |
|---|---|---|
| Layer | 7 | 4 |
| Main protocols | HTTP/HTTPS | TCP/UDP/TLS |
| Content-based routing | Yes | No HTTP-style routing |
| Path-based routing | Yes | No |
| Static IP requirement | Not the main use case | Strong use case |
| Performance focus | Application routing | Very high performance / low latency |
| Microservices | Excellent fit | Can be used when L4 behavior is required |

### Golden memory

```text
ALB
→ Application
→ HTTP/HTTPS
→ Layer 7
→ Smart routing

NLB
→ Network
→ TCP/UDP/TLS
→ Layer 4
→ Performance + static IP
```

---

# 19. GWLB — Gateway Load Balancer

**Gateway Load Balancer (GWLB)** is designed for deploying and scaling virtual network appliances.

Examples:

- Firewalls
- Intrusion detection systems
- Intrusion prevention systems
- Deep packet inspection appliances

---

# 20. GWLB Architecture

Think:

```text
Users
   ↓
GWLB
   ↓
Virtual Appliances
   ↓
GWLB
   ↓
Application
```

The appliances inspect the traffic.

For example:

```text
Traffic
   ↓
Firewall
   ↓
Allowed?
 ┌─┴─┐
No  Yes
↓    ↓
Drop Application
```

---

# 21. GWLB Key Concepts

GWLB has two important roles:

```text
1. Transparent network gateway
2. Load balancer for virtual appliances
```

### Exam clue

```text
Firewall
+
IDS/IPS
+
Traffic inspection
+
GENEVE 6081
        ↓
GWLB
```

The course intentionally kept GWLB high-level because the detailed networking behavior is more advanced.

---

# 22. Sticky Sessions

**Sticky Sessions**, also called **session affinity**, keep a client connected to the same backend target for a period of time.

Without stickiness:

```text
Request 1 → EC2-A
Request 2 → EC2-B
Request 3 → EC2-C
```

With stickiness:

```text
Client
   ↓
EC2-A

Request 1 → EC2-A
Request 2 → EC2-A
Request 3 → EC2-A
```

---

# 23. Why Use Sticky Sessions?

Suppose the application stores session information locally on an EC2 instance.

```text
User Login
   ↓
EC2-A
   ↓
Session stored
```

If the next request goes to EC2-B:

```text
Request
   ↓
EC2-B
   ↓
Session not available
```

This can cause problems.

Sticky sessions can keep that client routed to EC2-A for the configured stickiness period.

---

# 24. Disadvantage of Sticky Sessions

Stickiness can create an uneven traffic distribution.

Example:

```text
EC2-A → many sticky users
EC2-B → few users
EC2-C → few users
```

Therefore:

> **Sticky sessions improve session affinity but can reduce load distribution efficiency.**

For modern distributed applications, storing session state in a shared external system can reduce dependence on sticky sessions.

---

# 25. Sticky Session Cookies

The course introduced two broad cookie approaches:

```text
Sticky Sessions
├── Load-balancer-generated cookie
└── Application-based cookie
```

The cookie allows the load balancer to maintain affinity.

Conceptually:

```text
First request
   ↓
Load Balancer
   ↓
EC2-A
   ↓
Cookie returned

Next request
   ↓
Cookie sent
   ↓
Load Balancer
   ↓
EC2-A
```

You do not need to memorize every AWS cookie name from this lecture.

### Remember

```text
Sticky Session
→ Cookie
→ Same target
→ For a configured duration
```

---

# 26. Cross-Zone Load Balancing

Cross-Zone Load Balancing determines how traffic is distributed across targets in different Availability Zones.

Example:

```text
AZ-A
ALB Node
EC2
EC2

AZ-B
ALB Node
EC2
EC2
EC2
EC2
```

With cross-zone balancing, a load balancer node can distribute traffic across targets in other enabled AZs.

Conceptually:

```text
ALB Node A ─┬─ EC2-A1
            ├─ EC2-A2
            ├─ EC2-B1
            ├─ EC2-B2
            └─ EC2-B3
```

This helps distribute traffic more evenly when target counts are uneven between AZs.

---

# 27. Cross-Zone Example

Suppose:

```text
AZ-A → 2 EC2
AZ-B → 8 EC2
```

Without cross-zone balancing, traffic can remain more local to the load balancer nodes/AZs.

With cross-zone balancing:

```text
All registered healthy targets
        ↓
Traffic distributed across them
```

This can reduce imbalance caused by different numbers of targets in different AZs.

---

# 28. Important Cross-Zone Exam Concept

For the course's behavior:

```text
ALB
→ Enabled by default

NLB
→ Disabled by default

GWLB
→ Disabled by default
```

When studying exact pricing or current implementation details, verify against current AWS documentation because AWS networking behavior and pricing can change.

### Easy memory

```text
ALB
→ Cross-zone ON by default

NLB/GWLB
→ Cross-zone OFF by default
```

---

# 29. SSL/TLS Certificates

A website using HTTPS encrypts traffic **in transit**.

```text
Client
   ↓
HTTPS
   ↓
Load Balancer
```

The load balancer can perform **TLS termination**.

```text
Client
   ↓
HTTPS
   ↓
ALB
   ↓
HTTP
   ↓
EC2
```

In this architecture, encryption is terminated at the load balancer.

---

# 30. AWS Certificate Manager — ACM

AWS Certificate Manager (ACM) can be used to manage certificates for supported AWS services.

The course's load balancer architecture was:

```text
Client
   ↓
HTTPS : 443
   ↓
ALB
   ↓
Target Group
   ↓
EC2
```

The HTTPS listener requires a certificate.

---

# 31. SSL/TLS Listener

An ALB can have:

```text
HTTP : 80
HTTPS : 443
```

A common architecture is:

```text
HTTP
 ↓
Redirect
 ↓
HTTPS
 ↓
Target Group
```

For HTTPS, the load balancer needs a certificate.

---

# 32. SNI — Server Name Indication

SNI solves the problem of using **multiple certificates on the same load balancer**.

Example:

```text
One ALB
│
├── example.com
│   └── Certificate A
│
└── example.org
    └── Certificate B
```

The client indicates the hostname during the TLS handshake.

The load balancer can then select the appropriate certificate.

### Exam clue

```text
Multiple domains
+
Multiple certificates
+
Same load balancer
        ↓
SNI
```

The course emphasizes SNI with modern-generation load balancers.

---

# 33. SNI Simple Flow

```text
Client
   ↓
"I want example.com"
   ↓
ALB
   ↓
Select certificate for example.com
   ↓
TLS connection established
```

For another hostname:

```text
Client
   ↓
"I want example.org"
   ↓
ALB
   ↓
Select certificate for example.org
```

---

# 34. Connection Draining / Deregistration Delay

When an instance is being removed from service, we don't necessarily want to immediately terminate active connections.

The load balancer can give existing requests time to complete.

For ALB/NLB this is commonly called:

> **Deregistration Delay**

For the older CLB terminology:

> **Connection Draining**

---

# 35. Why Deregistration Delay Matters

Suppose:

```text
EC2-A
→ Long-running request
```

The instance needs to be removed.

Without graceful draining:

```text
Remove EC2-A
   ↓
Active request interrupted
```

With draining:

```text
Remove from new traffic
        ↓
Existing connections continue
        ↓
Requests complete
        ↓
Instance removed
```

### Easy memory

> **Stop new traffic, finish existing traffic.**

---

# 36. Auto Scaling Group — ASG

An **Auto Scaling Group (ASG)** automatically manages the number of EC2 instances in a group according to configured capacity and scaling policies.

The main goals are:

```text
High Availability
+
Scalability
```

ASG can:

```text
Scale Out
→ Add EC2 instances

Scale In
→ Remove EC2 instances
```

---

# 37. ASG Minimum, Desired and Maximum Capacity

An ASG normally uses three important capacity values:

```text
Minimum
Desired
Maximum
```

Example:

```text
Min     = 2
Desired = 4
Max     = 8
```

Normal state:

```text
4 EC2 instances
```

High workload:

```text
4 → 5 → 6 → 7 → 8
```

Low workload:

```text
4 → 3 → 2
```

The ASG maintains capacity within the configured limits.

---

# 38. ASG and Load Balancer

ASG and ELB work extremely well together.

```text
Users
  ↓
ALB
  ↓
Target Group
  ↓
ASG
  ├── EC2
  ├── EC2
  └── EC2
```

When the ASG launches a new instance:

```text
New EC2
   ↓
Registered in Target Group
   ↓
ALB can send traffic
```

When the ASG terminates an instance:

```text
EC2 terminated
   ↓
Deregistered from Target Group
```

---

# 39. ASG Can Replace Unhealthy Instances

One of the most important ASG features is instance replacement.

Example:

```text
ASG
├── EC2-A ✅
├── EC2-B ❌
└── EC2-C ✅
```

If EC2-B becomes unhealthy and the configured health checks report it as unhealthy:

```text
EC2-B
   ↓
Terminate
   ↓
Launch replacement
   ↓
Register with Target Group
```

---

# 40. Launch Template

An ASG needs instructions for creating EC2 instances.

The modern mechanism is:

> **Launch Template**

A launch template can define things such as:

- AMI
- Instance type
- Security Groups
- User Data
- EBS configuration
- IAM instance profile
- Key pair
- Other EC2 launch settings

Think:

```text
Launch Template
       ↓
"How should my EC2 instances be created?"
```

---

# 41. ASG Architecture

The overall architecture is:

```text
                    Users
                      |
                      v
                   ALB
                      |
                      Target Group
                      |
              -----------------
              |       |       |
             EC2     EC2     EC2
              \       |       /
               \      |      /
                \     |     /
                  ASG
                   |
              Scaling Policies
                   |
              CloudWatch Metrics
```

This is one of the most important architectures in this section.

---

# 42. ASG and Multi-AZ

For high availability, an ASG can launch EC2 instances in multiple Availability Zones.

```text
Region
│
├── AZ-A → EC2
│
├── AZ-B → EC2
│
└── AZ-C → EC2
```

This provides both:

```text
Horizontal Scaling
+
High Availability
```

---

# 43. ASG Hands-On Flow

The course hands-on used:

```text
Create Launch Template
        ↓
Select AMI
        ↓
Select Instance Type
        ↓
Configure Security Group
        ↓
Add User Data
        ↓
Create ASG
        ↓
Select multiple AZs
        ↓
Attach Target Group
        ↓
Configure Min / Desired / Max
        ↓
Create ASG
```

The ASG then automatically launched EC2 instances.

---

# 44. ASG Hands-On — Scaling Out

Suppose:

```text
Desired = 1
```

Change:

```text
Desired = 2
```

The ASG detects that current capacity is below desired capacity:

```text
Current = 1
Desired = 2
        ↓
Launch EC2
        ↓
Current = 2
```

The new instance is automatically registered in the target group when integrated with ELB.

---

# 45. ASG Hands-On — Scaling In

Suppose:

```text
Current = 2
Desired = 1
```

ASG removes capacity:

```text
Current = 2
Desired = 1
        ↓
Terminate one EC2
        ↓
Current = 1
```

The terminated instance is removed from the load balancer target group.

---

# 46. ASG Scaling Policies

ASG scaling policies tell AWS **when and how to change capacity**.

The course introduced:

```text
Scaling Policies
├── Dynamic
│   ├── Target Tracking
│   ├── Step Scaling
│   └── Simple Scaling
│
├── Scheduled
│
└── Predictive
```

---

# 47. Target Tracking Scaling

Target Tracking is one of the easiest scaling policies to understand.

You define:

```text
Metric
+
Target value
```

Example:

```text
Average CPU Utilization
Target = 40%
```

The ASG automatically scales to maintain the metric around that target.

```text
CPU too high
   ↓
Scale Out

CPU too low
   ↓
Scale In
```

### Memory trick

```text
Target Tracking
→ "Keep metric around X%"
```

---

# 48. Target Tracking Example

Suppose:

```text
Target CPU = 40%
```

Current situation:

```text
CPU = 80%
```

ASG may:

```text
Add EC2
   ↓
More capacity
   ↓
CPU utilization decreases
```

If workload later drops:

```text
CPU = 10%
   ↓
Scale In
   ↓
Fewer EC2 instances
```

---

# 49. Step Scaling

Step Scaling uses CloudWatch alarms and different scaling adjustments depending on how far the metric crosses a threshold.

Example:

```text
CPU 50–70%
→ Add 1 instance

CPU 70–90%
→ Add 2 instances

CPU > 90%
→ Add 4 instances
```

Think:

> **The more severe the condition, the larger the scaling action.**

---

# 50. Simple Scaling

Simple Scaling uses an alarm to trigger a scaling action.

Example:

```text
CPU alarm
   ↓
Add 2 instances
```

Or:

```text
Low CPU alarm
   ↓
Remove 1 instance
```

For the interview, understand the conceptual difference:

```text
Simple
→ One scaling action

Step
→ Different actions for different alarm levels
```

---

# 51. Scheduled Scaling

Scheduled scaling is useful when future workload is predictable.

Example:

```text
Every Friday 5 PM
        ↓
Expected traffic increase
        ↓
Increase capacity
```

Another example:

```text
00:00
→ Reduce capacity

09:00
→ Increase capacity
```

### Memory trick

```text
Known schedule
→ Scheduled Scaling
```

---

# 52. Predictive Scaling

Predictive Scaling uses historical workload patterns to forecast future demand.

Conceptually:

```text
Historical metrics
       ↓
Forecast
       ↓
Expected future load
       ↓
Scale ahead of time
```

This is useful when workload has predictable recurring patterns.

### Memory trick

```text
Scheduled
→ You know the schedule

Predictive
→ AWS forecasts the schedule
```

---

# 53. Common Scaling Metrics

The course discussed several possible metrics.

## CPU Utilization

Common metric for compute-heavy workloads.

```text
High CPU
→ Add capacity
```

## Request Count Per Target

Useful when application load is better represented by incoming requests than CPU.

```text
Too many requests per target
        ↓
Scale Out
```

## Network In / Network Out

Useful for network-bound workloads.

## Custom CloudWatch Metrics

Applications can publish their own metrics.

```text
Application
   ↓
Custom Metric
   ↓
CloudWatch
   ↓
ASG Scaling
```

---

# 54. Scaling Cooldown

A scaling cooldown provides time for the system to stabilize after a scaling action.

Conceptually:

```text
Scaling action
      ↓
Cooldown
      ↓
Allow metrics to stabilize
      ↓
Evaluate again
```

The course described a default cooldown of 300 seconds.

For real production configuration, verify the current AWS behavior and use modern ASG policy guidance rather than assuming the old course defaults apply everywhere.

### Why cooldown?

Suppose:

```text
CPU = 90%
```

ASG adds instances.

Immediately checking the metric again may still show high CPU because the new instances haven't finished initializing.

Cooldown helps avoid unnecessary repeated scaling actions.

---

# 55. Ready-to-Use AMIs and Scaling

The course emphasized using preconfigured AMIs.

Without a prepared AMI:

```text
Launch EC2
 ↓
Install software
 ↓
Configure application
 ↓
Start service
```

With a ready AMI:

```text
Launch EC2
 ↓
Application already prepared
 ↓
Serve traffic faster
```

This reduces the time between:

```text
Scale Out
   ↓
New instance ready
```

and can improve the effectiveness of automatic scaling.

---

# 56. Detailed Monitoring and Scaling

The course also mentioned faster CloudWatch metrics through detailed monitoring.

The basic idea:

```text
More frequent metrics
        ↓
Faster visibility
        ↓
Faster scaling decisions
```

Exact monitoring intervals and current AWS capabilities should be checked against current CloudWatch/EC2 documentation when configuring production systems.

---

# 57. ALB vs NLB vs GWLB — Most Important Table

| Feature | ALB | NLB | GWLB |
|---|---|---|---|
| OSI layer | Layer 7 | Layer 4 | Layer 3 |
| Main purpose | Application routing | High-performance network traffic | Network appliance inspection |
| Common protocols | HTTP/HTTPS | TCP/UDP/TLS | IP / GENEVE |
| Path routing | Yes | No | No |
| Static IP focus | No | Yes | Not the primary feature |
| Typical use | Web apps, APIs, microservices | TCP/UDP, high performance | Firewalls, IDS/IPS |
| Target concept | Target groups | Target groups | Target groups |
| Exam clue | `/users`, host header | TCP/UDP/static IP | Firewall/inspection/6081 |

### Golden memory

```text
ALB
→ Application

NLB
→ Network

GWLB
→ Security Appliances
```

---

# 58. ELB vs ASG

This is a very common interview confusion.

```text
ELB
→ Distributes traffic

ASG
→ Manages number of EC2 instances
```

Combined:

```text
Users
   ↓
ELB
   ↓
ASG-managed EC2 instances
```

### Simple analogy

```text
ELB
→ Traffic Manager

ASG
→ Server Manager
```

---

# 59. ELB Does Not Automatically Create EC2 Instances

A load balancer can distribute traffic among registered targets.

It does **not** by itself provide automatic EC2 fleet scaling.

For automatic EC2 scaling:

```text
ELB
+
ASG
```

The ASG handles instance count.

---

# 60. ASG Does Not Replace the Load Balancer

An ASG can create and terminate instances.

But clients still need a way to reach the fleet.

```text
ASG
→ Creates/manages EC2

ALB
→ Receives traffic and distributes it
```

Together:

```text
Client
   ↓
ALB
   ↓
Target Group
   ↓
ASG EC2 instances
```

---

# 61. Most Important Architecture to Remember

```text
                         Internet
                            |
                            v
                    Application Load
                       Balancer
                            |
                         Listener
                            |
                     Target Group
                            |
             ---------------------------
             |            |            |
            EC2          EC2          EC2
             \            |            /
              \           |           /
               ------ Auto Scaling ------
                        Group
                            |
                     Scaling Policy
                            |
                        CloudWatch
```

This architecture provides:

```text
ALB
→ Traffic distribution

Target Group
→ Backend target management

ASG
→ Instance scaling/replacement

Multi-AZ
→ High availability
```

---

# 62. Common Confusions

## Scalability vs High Availability

```text
Scalability
→ Handle more/less workload

High Availability
→ Continue operating during failures
```

---

## Scale Up vs Scale Out

```text
Scale Up
→ Bigger EC2

Scale Out
→ More EC2
```

---

## ALB vs NLB

```text
ALB
→ Layer 7
→ HTTP/HTTPS
→ Path/host routing

NLB
→ Layer 4
→ TCP/UDP/TLS
→ High performance/static IP
```

---

## ALB vs GWLB

```text
ALB
→ Routes application requests

GWLB
→ Routes traffic through virtual appliances
```

---

## ELB vs ASG

```text
ELB
→ Distribute traffic

ASG
→ Add/remove/replace EC2 instances
```

---

## Sticky Sessions vs Load Balancing

```text
Normal
→ Requests can go to different targets

Sticky
→ Client remains associated with same target
```

---

## Connection Draining vs Health Check

```text
Health Check
→ Is target healthy?

Deregistration Delay
→ Give existing requests time to finish
```

---

## Launch Template vs ASG

```text
Launch Template
→ How to launch EC2

ASG
→ How many EC2 instances to maintain
```

---

# 63. How to Explain High Availability and Scalability in an Interview

### Question: What is the difference between scalability and high availability?

> "Scalability is the ability of an application to handle changing workload by increasing or decreasing resources. Vertical scaling means increasing the size of an instance, while horizontal scaling means adding more instances. High availability is about keeping the application available even when infrastructure fails, commonly by distributing resources across multiple Availability Zones."

---

# 64. How to Explain ELB in an Interview

### Question: What is an Elastic Load Balancer?

> "Elastic Load Balancing distributes incoming traffic across multiple healthy backend targets. It provides a single endpoint for clients, performs health checks, and prevents traffic from being sent to unhealthy targets. It also integrates well with services such as Auto Scaling."

---

# 65. How to Explain ALB in an Interview

### Question: What is an ALB?

> "An Application Load Balancer operates at Layer 7 and is designed for HTTP and HTTPS traffic. It supports content-based routing such as path-based and host-based routing, so it is commonly used for web applications, APIs, microservices, and container-based applications."

---

# 66. How to Explain NLB in an Interview

### Question: What is an NLB?

> "A Network Load Balancer operates at Layer 4 and is designed for TCP, UDP, and TLS traffic. It provides very high performance and low latency and can provide fixed IP addresses, which is useful when clients require static IPs."

---

# 67. How to Explain GWLB in an Interview

### Question: What is a Gateway Load Balancer?

> "A Gateway Load Balancer is used to deploy and scale virtual network appliances such as firewalls and intrusion detection systems. It transparently sends network traffic through those appliances for inspection and processing."

---

# 68. How to Explain Sticky Sessions

### Question: What are sticky sessions?

> "Sticky sessions, or session affinity, ensure that requests from a client continue to go to the same backend target for a configured period. This can be useful when session state is stored locally on the backend, although it can also lead to uneven traffic distribution."

---

# 69. How to Explain Cross-Zone Load Balancing

### Question: What is cross-zone load balancing?

> "Cross-zone load balancing allows load balancer nodes to distribute traffic across targets in other Availability Zones rather than restricting traffic to targets in the same zone. This can help distribute traffic more evenly when the number of targets differs between Availability Zones."

---

# 70. How to Explain TLS Termination

### Question: What is TLS termination on a load balancer?

> "TLS termination means the load balancer handles the HTTPS/TLS connection from the client, decrypts the traffic, and then forwards the request to the backend according to the configured architecture. The certificate is associated with the load balancer's HTTPS listener."

---

# 71. How to Explain SNI

### Question: What is SNI?

> "Server Name Indication allows the client to indicate the hostname during the TLS handshake. This allows a load balancer to select the appropriate certificate when multiple domains and certificates are configured on the same load balancer."

---

# 72. How to Explain ASG

### Question: What is an Auto Scaling Group?

> "An Auto Scaling Group manages a fleet of EC2 instances and automatically maintains the configured capacity. It can scale out when demand increases, scale in when demand decreases, and replace unhealthy instances. When integrated with a load balancer, newly launched instances can automatically register with the target group."

---

# 73. How to Explain Target Tracking

### Question: What is target tracking scaling?

> "Target tracking scaling lets us define a target value for a metric, such as 40 percent average CPU utilization. The Auto Scaling Group then automatically adds or removes instances to keep the metric around that target."

---

# 74. Scenario-Based Interview Questions

## Q1. Traffic to your website suddenly increases. What AWS architecture would you use?

### Answer

> "I would use an Application Load Balancer in front of an Auto Scaling Group. The ALB distributes traffic across healthy instances, while the ASG scales the number of instances according to workload."

---

## Q2. You need HTTP path-based routing for microservices. Which load balancer?

### Answer

> "I would use an Application Load Balancer because it supports Layer 7 HTTP/HTTPS routing, including path-based and host-based routing."

---

## Q3. Your application requires UDP traffic and very low latency. Which load balancer?

### Answer

> "I would consider a Network Load Balancer because it operates at Layer 4 and supports UDP with high performance and low latency."

---

## Q4. Your company needs all network traffic inspected by a firewall appliance. Which load balancer?

### Answer

> "I would consider a Gateway Load Balancer because it is designed to transparently route traffic through virtual network appliances such as firewalls and intrusion detection systems."

---

## Q5. Your clients require a fixed set of IP addresses for your application.

### Answer

> "I would consider a Network Load Balancer because it supports fixed IP addresses per enabled Availability Zone and can use Elastic IP addresses."

---

## Q6. One EC2 instance behind your ALB becomes unhealthy. What happens?

### Answer

> "The load balancer health check detects that the target is unhealthy and stops sending new traffic to it. If the target is managed by an Auto Scaling Group and ELB health checks are enabled for the ASG, the ASG can terminate and replace that unhealthy instance."

---

## Q7. You want clients to remain connected to the same backend instance.

### Answer

> "I would consider enabling sticky sessions. The load balancer uses cookies to maintain affinity between the client and the backend target for the configured duration."

---

## Q8. An application has long-running requests and an instance needs to be removed.

### Answer

> "I would use deregistration delay so the load balancer stops sending new requests to the target while allowing existing requests to complete before the instance is removed."

---

## Q9. Your workload is predictable every Friday evening.

### Answer

> "I could use scheduled scaling because the workload increase is predictable in advance."

---

## Q10. Your workload follows a recurring pattern that AWS can forecast.

### Answer

> "Predictive scaling can use historical workload patterns to forecast future demand and scale ahead of the expected load."

---

## Q11. CPU utilization should stay around 40%.

### Answer

> "Target tracking scaling is a good fit because I can define average CPU utilization as the metric and 40 percent as the target."

---

## Q12. CPU levels have different severity levels and each should add a different number of instances.

### Answer

> "I would consider step scaling because it allows different scaling adjustments for different alarm levels."

---

# 75. Troubleshooting Load Balancer Targets

When a target is unhealthy, do not immediately blame the load balancer.

Use this flow:

```text
1. Is EC2 running?
        ↓
2. Is application running?
        ↓
3. Is application listening on expected port?
        ↓
4. Is health check port correct?
        ↓
5. Is health check path correct?
        ↓
6. Does Security Group allow the traffic?
        ↓
7. Is the target registered correctly?
        ↓
8. Are listener/routing rules correct?
```

### Example

```text
Target unhealthy
      ↓
Check health check
      ↓
Check SG
      ↓
Check application
      ↓
Check port
      ↓
Check routing
```

This is an excellent interview troubleshooting flow.

---

# 76. Troubleshooting ASG

If an ASG continuously launches and terminates instances:

```text
ASG
 ↓
Launch EC2
 ↓
Health check fails
 ↓
Terminate EC2
 ↓
Launch replacement
 ↓
Health check fails again
```

Investigate:

```text
Security Group
User Data
Application startup
Health Check Path
Health Check Port
Target Group
AMI
Launch Template
```

A common course demonstration issue was that incorrect Security Group configuration or User Data could prevent newly launched instances from becoming healthy.

---

# 77. Hands-On Scenarios to Practice

## Scenario 1 — Build an ALB

Create:

```text
2 EC2 Instances
        ↓
Target Group
        ↓
Application Load Balancer
```

Verify:

```text
ALB DNS
   ↓
Application response
```

Refresh multiple times and observe requests reaching multiple healthy targets.

---

## Scenario 2 — ALB Health Check

Stop one EC2 instance.

Observe:

```text
EC2
 ↓
Unhealthy
 ↓
Removed from active traffic
```

Start it again.

Observe:

```text
Initial
 ↓
Healthy
 ↓
Traffic resumes
```

---

## Scenario 3 — Secure ALB → EC2

Configure:

```text
ALB SG
→ HTTP/HTTPS from Internet

EC2 SG
→ Application port only from ALB SG
```

Then test:

```text
Direct EC2 access → Blocked
ALB access        → Works
```

---

## Scenario 4 — Path-Based Routing

Create two target groups:

```text
TG-Users
TG-Search
```

Configure:

```text
/users
   ↓
TG-Users

/search
   ↓
TG-Search
```

Test each path.

---

## Scenario 5 — NLB

Create:

```text
NLB
 ↓
TCP Listener
 ↓
Target Group
 ↓
EC2
```

Check:

```text
NLB IP/DNS
 ↓
Application
```

---

## Scenario 6 — Sticky Sessions

Enable sticky sessions.

Then test:

```text
Request 1 → EC2-A
Request 2 → EC2-A
Request 3 → EC2-A
```

Disable stickiness and observe normal request distribution.

---

## Scenario 7 — ASG

Create:

```text
Launch Template
      ↓
ASG
      ↓
Min = 1
Desired = 1
Max = 3
```

Observe the EC2 instance being automatically created.

---

## Scenario 8 — ASG Scale Out

Change:

```text
Desired
1 → 2
```

Observe:

```text
New EC2
   ↓
Target Group
   ↓
ALB
```

---

## Scenario 9 — ASG Scale In

Change:

```text
Desired
2 → 1
```

Observe one instance being terminated and deregistered.

---

## Scenario 10 — Target Tracking

Create:

```text
Target Tracking
Metric = Average CPU
Target = 40%
```

Generate CPU load on an EC2 instance.

Observe:

```text
CPU increases
   ↓
CloudWatch
   ↓
Scaling Policy
   ↓
ASG Scale Out
```

Stop the load and observe eventual scale-in behavior.

---

# 78. Quick Revision Cheat Sheet

```text
SCALABILITY
→ Handle changing workload

VERTICAL SCALING
→ Bigger instance
→ Scale Up / Down

HORIZONTAL SCALING
→ More/fewer instances
→ Scale Out / In

HIGH AVAILABILITY
→ Survive failures
→ Multi-AZ

ELB
→ Distribute traffic
→ Health checks
→ Single endpoint

ALB
→ Layer 7
→ HTTP / HTTPS
→ Path routing
→ Host routing
→ Microservices

NLB
→ Layer 4
→ TCP / UDP / TLS
→ High performance
→ Static IP support

GWLB
→ Layer 3
→ Security appliances
→ Firewall / IDS / IPS
→ GENEVE 6081

TARGET GROUP
→ Group of backend targets
→ Health checks
→ ALB/NLB/GWLB routing target

LISTENER
→ Accepts connections
→ Port + protocol

LISTENER RULE
→ Condition + action

STICKY SESSIONS
→ Same client → Same target
→ Cookie

CROSS-ZONE
→ Distribute across AZs

TLS TERMINATION
→ Load balancer handles HTTPS/TLS

SNI
→ Multiple certificates/domains
→ Same load balancer

DEREGISTRATION DELAY
→ Stop new traffic
→ Finish existing requests

ASG
→ Manage EC2 fleet
→ Scale Out / In
→ Replace unhealthy instances

LAUNCH TEMPLATE
→ Instructions for launching EC2

MIN
→ Minimum instances

DESIRED
→ Target number of instances

MAX
→ Maximum instances

TARGET TRACKING
→ Maintain metric around target

STEP SCALING
→ Different actions for different alarm levels

SIMPLE SCALING
→ Alarm triggers scaling action

SCHEDULED SCALING
→ Known future workload

PREDICTIVE SCALING
→ Forecast future workload

CLOUDWATCH
→ Metrics/alarms can drive scaling
```

---

# ⭐ 30-Second Section Summary

> **Scalability allows an application to handle changing workload, while High Availability helps it continue operating during failures. Vertical scaling means increasing instance size, while horizontal scaling means adding more instances. Elastic Load Balancing distributes traffic across healthy targets. ALB operates at Layer 7 and supports HTTP/HTTPS content-based routing, NLB operates at Layer 4 for high-performance TCP/UDP/TLS workloads and static IP requirements, and GWLB is used for network security appliances. Auto Scaling Groups manage the number of EC2 instances and can scale out, scale in, and replace unhealthy instances. Together, ALB + Target Group + ASG + Multi-AZ provide a common highly available and scalable AWS architecture.**

---

# 🧠 Golden Memory Map

```text
                 AWS HIGH AVAILABILITY
                         |
             -------------------------
             |                       |
        LOAD BALANCING             SCALING
             |                       |
      ----------------          ------------
      |        |       |         |          |
     ALB      NLB     GWLB      ASG      Policies
      |        |       |         |          |
   Layer 7   Layer 4 Layer 3   EC2 Fleet   |
   HTTP      TCP/UDP  Security               |
   Routing   Static   Appliances             |
                                             |
                           -------------------------------
                           |            |         |       |
                       Target       Step     Scheduled Predictive
                       Tracking
```

## The easiest way to remember the whole section

```text
ALB
→ "Which application/service should get this request?"

NLB
→ "Which network target should get this TCP/UDP traffic?"

GWLB
→ "Which security appliance should inspect this traffic?"

ASG
→ "How many EC2 instances should I have?"

Target Group
→ "Which backend targets belong together?"

Health Check
→ "Is this target healthy?"

Sticky Session
→ "Keep this client with the same target."

Deregistration Delay
→ "Stop new traffic, let old traffic finish."

SNI
→ "Which certificate belongs to this hostname?"

Target Tracking
→ "Keep my metric around this value."
```

# ⭐ Most Important Interview Points

```text
1. Scalability ≠ High Availability

2. Vertical = Bigger Instance

3. Horizontal = More Instances

4. ALB = Layer 7 = HTTP/HTTPS

5. NLB = Layer 4 = TCP/UDP/TLS

6. GWLB = Layer 3 = Security Appliances

7. ALB supports path/host/query-based routing

8. Target Groups contain backend targets

9. Health Checks prevent traffic going to unhealthy targets

10. Sticky Sessions use cookies to maintain session affinity

11. Cross-Zone Load Balancing distributes traffic across AZs

12. TLS termination can happen at the load balancer

13. SNI allows multiple certificates on supported load balancers

14. Deregistration Delay lets existing requests finish

15. ASG manages EC2 instance count

16. Launch Template defines how EC2 instances are launched

17. Min / Desired / Max define ASG capacity boundaries

18. ASG can replace unhealthy instances

19. Target Tracking maintains a metric around a target value

20. Scheduled Scaling is for predictable schedules

21. Predictive Scaling uses forecasts

22. ALB + ASG is a core scalable web architecture
```

## Final Mental Picture

```text
                        USERS
                          |
                          v
                     ┌─────────┐
                     │   ALB   │
                     └────┬────┘
                          |
                     Target Group
                          |
             ┌────────────┼────────────┐
             |            |            |
            EC2          EC2          EC2
             \            |            /
              \           |           /
               └─────── ASG ─────────┘
                       |
              Scaling Policies
                       |
                  CloudWatch
```

> **Traffic problem → ELB**  
> **Server-count problem → ASG**  
> **Failure problem → Multi-AZ + Health Checks**  
> **HTTP routing problem → ALB**  
> **TCP/UDP + static IP problem → NLB**  
> **Firewall/inspection problem → GWLB**
