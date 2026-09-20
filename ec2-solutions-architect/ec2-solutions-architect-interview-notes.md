# AWS EC2 — Solutions Architect Associate Level — Interview Notes

> **Course:** Section 6 — EC2 - Solutions Architect Associate Level  
> **Lectures:** 48–56  
> **Scope:** Private/Public/Elastic IP, Placement Groups, ENI, and EC2 Hibernate  
> **Goal:** Easy understanding + hands-on revision + interview preparation

---

# 1. Private IP vs Public IP vs Elastic IP

## Private IPv4

A **Private IP** is used for communication inside a private network such as a VPC.

```text
EC2
 ↓
Private IP
 ↓
Private AWS Network
```

Important points:

- Used for communication inside the VPC/private network.
- Must be unique within the relevant private network.
- Private IPs are not directly reachable from the public Internet.
- EC2 keeps its private IP when the instance is stopped and started.
- Private IP communication is commonly used between EC2 instances and other AWS resources.

### Example

```text
EC2-A
Private IP: 10.0.1.10
       ↕
EC2-B
Private IP: 10.0.1.20
```

The instances can communicate using their private IPs when network/security configuration allows it.

---

## Public IP

A **Public IPv4 address** allows an EC2 instance to communicate over the public Internet when the required networking configuration is in place.

```text
Internet
   ↓
Public IPv4
   ↓
EC2
```

Important points:

- Public IPv4 addresses must be globally unique while assigned.
- They can change when an EC2 instance is **stopped and started**.
- A public IP is different from the instance's private IP.
- Public Internet connectivity also requires the appropriate VPC routing and Security Group configuration.

### Important behavior

```text
Start EC2
   ↓
Public IP = 18.x.x.x

Stop + Start
   ↓
Public IP may become 3.x.x.x
```

The **private IP normally remains unchanged**.

---

# 2. Elastic IP

An **Elastic IP (EIP)** is a static public IPv4 address allocated to your AWS account.

It is useful when you specifically need a stable public IPv4 address.

```text
Elastic IP
     ↓
EC2 Instance
```

If the instance is stopped and started:

```text
EC2
 ↓
Elastic IP remains associated
 ↓
Public IP remains the same
```

An Elastic IP can be associated with an EC2 instance or network interface and can be moved when needed.

### Why use an Elastic IP?

One use case is quickly moving a stable public IP from one instance to another during a failover scenario.

### Important AWS design point

Avoid using Elastic IPs as a default architecture for applications when DNS or a Load Balancer can solve the problem more appropriately.

For production architectures, a common pattern is:

```text
User
 ↓
DNS
 ↓
Load Balancer
 ↓
EC2
```

rather than relying directly on a fixed public IP.

### Cost awareness

AWS charges for public IPv4 usage, so don't leave unused public IPv4 addresses or Elastic IPs allocated unnecessarily.

---

# 3. Private vs Public vs Elastic IP

| Feature | Private IP | Public IP | Elastic IP |
|---|---|---|---|
| Used for | Private networking | Internet connectivity | Stable public IPv4 |
| Internet reachable directly | No | Yes, with proper networking | Yes, with proper networking |
| Changes after Stop/Start | Normally no | Can change | Remains stable while associated |
| Scope | Private network/VPC | Public Internet | Public Internet |
| Typical use | Internal communication | Temporary Internet access | Stable public IPv4 requirement |

### Easy memory

```text
Private IP
→ Internal communication

Public IP
→ Internet communication

Elastic IP
→ Static public IPv4
```

---

# 4. Important IP Behavior

### Normal EC2

```text
EC2
├── Private IP → normally stays the same
└── Public IP  → can change after Stop + Start
```

### With Elastic IP

```text
EC2
├── Private IP → stays the same
└── Elastic IP → stays the same while associated
```

### Important distinction

A **reboot** is different from a **stop/start**.

```text
Reboot
→ Operating system restarts
→ Public IP normally remains

Stop + Start
→ Instance is stopped and started
→ Public IPv4 can change
```

---

# 5. EC2 Placement Groups

A **Placement Group** allows you to influence how EC2 instances are placed on AWS infrastructure.

There are three important placement strategies:

```text
Placement Groups
├── Cluster
├── Spread
└── Partition
```

---

# 6. Cluster Placement Group

A **Cluster Placement Group** places instances close together within a single Availability Zone.

Purpose:

> **High network performance and low network latency.**

```text
Availability Zone
┌─────────────────────────┐
│ EC2  EC2  EC2  EC2     │
│  ↕    ↕    ↕    ↕       │
│ High bandwidth/low      │
│ latency communication   │
└─────────────────────────┘
```

### Advantages

- Low network latency
- High network throughput
- Good for tightly coupled workloads

### Disadvantage

Because instances are concentrated in one Availability Zone, an AZ-level failure can affect the instances together.

### Use cases

- Big data processing
- HPC-style workloads
- Applications requiring very high network performance

### Memory trick

```text
Cluster
→ Keep instances CLOSE
→ Performance
→ Low latency
```

---

# 7. Spread Placement Group

A **Spread Placement Group** places instances on separate underlying hardware to reduce correlated hardware failures.

```text
Hardware 1 → EC2
Hardware 2 → EC2
Hardware 3 → EC2
Hardware 4 → EC2
```

The idea is:

> **Separate the failure risk.**

### Advantages

- Reduces the chance of multiple instances being affected by the same hardware failure.
- Suitable for critical applications where instance failures should be isolated.

### Limitation

Spread placement groups have a limit on the number of instances that can be placed per Availability Zone per placement group.

### Use cases

- Critical applications
- Small number of important instances
- Workloads where hardware failure isolation matters

### Memory trick

```text
Spread
→ Keep instances APART
→ Reduce correlated failure
```

---

# 8. Partition Placement Group

A **Partition Placement Group** spreads instances across multiple logical partitions.

Each partition is isolated from the hardware/rack used by another partition.

```text
AZ
├── Partition 1 → EC2 EC2 EC2
├── Partition 2 → EC2 EC2 EC2
└── Partition 3 → EC2 EC2 EC2
```

Partitions can span multiple Availability Zones within a Region.

### Advantages

- Hardware/rack-level failure isolation
- Can support large numbers of instances
- Suitable for distributed and partition-aware applications

### Use cases

- Hadoop
- Cassandra
- Kafka
- HDFS
- HBase

These applications can distribute data/workloads across partitions.

### Memory trick

```text
Partition
→ Spread into GROUPS
→ Large distributed workloads
→ Failure isolation between partitions
```

---

# 9. Placement Groups Comparison

| Strategy | Main goal | Placement | Typical use |
|---|---|---|---|
| Cluster | Performance | Close together | HPC, big data |
| Spread | Failure isolation | Separate hardware | Critical small workloads |
| Partition | Distributed failure isolation | Separate partitions/racks | Kafka, Cassandra, Hadoop |

### Easy interview memory

```text
Cluster  → CLOSE → Performance
Spread   → APART → Small critical workloads
Partition → GROUPS → Large distributed workloads
```

---

# 10. Elastic Network Interface — ENI

An **Elastic Network Interface (ENI)** is a logical virtual network interface in a VPC.

Think of it as a **virtual network card**.

```text
EC2
 ↓
ENI
 ↓
Network connectivity
```

An ENI can have attributes such as:

- Primary private IPv4 address
- One or more secondary private IPv4 addresses
- Public IPv4 address association
- Elastic IP association
- Security Groups
- MAC address

---

# 11. Primary and Secondary ENI

An EC2 instance normally has a primary ENI.

```text
EC2
 ↓
eth0
 ↓
Primary ENI
 ↓
Primary private IP
```

You can also attach another ENI:

```text
EC2
├── eth0 → Primary ENI
│          └── Primary private IP
│
└── eth1 → Secondary ENI
           └── Secondary private IP
```

A secondary ENI can provide additional network connectivity and IP addressing.

---

# 12. ENI and Availability Zone

An ENI is associated with a specific Availability Zone.

Therefore:

```text
ENI created in AZ-A
        ↓
Can be attached to
        ↓
EC2 in AZ-A
```

You cannot simply move the ENI across Availability Zones.

---

# 13. Why Would We Create an ENI Manually?

One useful advanced scenario is **network failover**.

Suppose:

```text
EC2-A
   ↑
   │
Secondary ENI
   │
   ↓
EC2-B
```

You can detach the ENI from EC2-A and attach it to EC2-B.

The ENI carries its private IP and network configuration with it.

```text
Before:

EC2-A
 ↓
ENI
 ↓
Private IP

After:

EC2-B
 ↓
Same ENI
 ↓
Same Private IP
```

This can provide a quick failover mechanism.

---

# 14. ENI Hands-On Flow

Typical workflow:

```text
EC2
 ↓
Network Interfaces
 ↓
Create Network Interface
 ↓
Choose Subnet
 ↓
Choose private IP configuration
 ↓
Attach Security Group
 ↓
Create ENI
 ↓
Attach ENI to EC2
```

Then:

```text
EC2-A
 ↓
Detach ENI
 ↓
Attach ENI
 ↓
EC2-B
```

The private IP associated with the ENI moves with it.

---

# 15. ENI vs EC2 Primary Interface

There is an important distinction:

```text
Primary ENI
→ Created/managed as part of the EC2 instance lifecycle

Manually created ENI
→ Can be managed independently
→ Can be moved between compatible EC2 instances in the same AZ
```

When an EC2 instance is terminated, its automatically created primary ENI is normally removed according to its lifecycle.

A separately created ENI can remain available after the instance is terminated.

---

# 16. EC2 Hibernate

**EC2 Hibernate** allows an instance to preserve its in-memory state before stopping.

Normal Stop:

```text
Running
 ↓
Stop
 ↓
RAM is lost
 ↓
Start
 ↓
OS boots again
 ↓
Application starts again
```

Hibernate:

```text
Running
 ↓
Hibernate
 ↓
RAM state saved to EBS
 ↓
Instance stops
 ↓
Start
 ↓
RAM state restored
 ↓
Continue from previous state
```

---

# 17. How EC2 Hibernate Works

Suppose:

```text
EC2
├── RAM
│   └── Application state
│
└── Root EBS
```

When hibernating:

```text
RAM
 ↓
Saved to root EBS
 ↓
Instance stops
```

When starting:

```text
Root EBS
 ↓
RAM state restored
 ↓
Instance continues
```

The goal is to avoid a complete operating-system/application initialization cycle.

---

# 18. Hibernate vs Stop vs Terminate

| State/action | RAM | EBS data | Instance |
|---|---|---|---|
| Stop | Lost | Preserved | Can start again |
| Hibernate | Preserved by saving state to EBS | Preserved | Can resume |
| Terminate | Lost | Root EBS may be deleted depending on configuration | Cannot restart |

### Easy memory

```text
STOP
→ Shut down

HIBERNATE
→ Save RAM state + stop

TERMINATE
→ Delete instance
```

---

# 19. EC2 Hibernate Requirements

Important requirements from this section:

- Root volume must be an **EBS volume**.
- Root EBS volume must be **encrypted**.
- Root volume must have enough space to store the RAM state.
- The instance type/OS must support hibernation.
- Hibernate has supported instance/configuration limits.

### Key concept

If an instance has:

```text
RAM = 1 GB
```

the root EBS volume needs enough free capacity to store the hibernation state.

---

# 20. Why Use Hibernate?

Hibernate is useful when:

- You want faster startup.
- Application initialization takes significant time.
- You want to preserve in-memory state.
- You have a long-running process that you want to pause and resume.

Example:

```text
Application running
       ↓
Large cache loaded in RAM
       ↓
Hibernate
       ↓
Start later
       ↓
RAM state restored
```

---

# 21. Hibernate Hands-On

Typical setup:

```text
Launch EC2
 ↓
Choose supported instance
 ↓
Enable hibernation
 ↓
Encrypt root EBS volume
 ↓
Ensure EBS has enough capacity for RAM
 ↓
Launch
```

Test:

```bash
uptime
```

Before hibernation:

```text
Instance uptime → 5 minutes
```

Hibernate:

```text
Running
 ↓
Hibernate
 ↓
Stopped
```

Start again:

```text
Start
 ↓
Connect
 ↓
uptime
```

The OS uptime can continue from before hibernation because the in-memory state was preserved.

---

# 22. Common Confusions

## Public IP vs Elastic IP

```text
Public IP
→ Can change after Stop + Start

Elastic IP
→ Static public IPv4 while associated
```

---

## Private IP vs Public IP

```text
Private IP
→ Internal/private network communication

Public IP
→ Public Internet connectivity
```

---

## Public IP vs Internet Gateway

A public IP alone doesn't guarantee Internet connectivity.

You also need appropriate networking, such as:

```text
EC2
 ↓
Subnet route table
 ↓
Internet Gateway
 ↓
Internet
```

plus appropriate Security Group rules.

---

## Security Group vs ENI

```text
Security Group
→ Firewall rules

ENI
→ Virtual network interface
```

A Security Group can be associated with an ENI.

---

## ENI vs Elastic IP

```text
ENI
→ Network interface

Elastic IP
→ Static public IPv4 address
```

An Elastic IP can be associated with an ENI.

---

## Stop vs Hibernate

```text
Stop
→ RAM state is lost

Hibernate
→ RAM state is saved to EBS
```

---

## Hibernate vs Terminate

```text
Hibernate
→ Can start/resume instance later

Terminate
→ Instance is deleted
```

---

# 23. How to Explain EC2 Networking in an Interview

If asked:

> **"What's the difference between private and public IP?"**

Say:

> "A private IP is used for communication within private AWS networking such as a VPC, while a public IP provides Internet-facing connectivity when the VPC routing and security configuration allow it. An EC2 public IPv4 can change after a stop and start, whereas an Elastic IP provides a stable public IPv4 address while it is allocated and associated."

---

# 24. How to Explain Placement Groups

If asked:

> **"What are EC2 Placement Groups?"**

Say:

> "Placement Groups allow us to influence how EC2 instances are placed on AWS infrastructure. Cluster is used when we need high network performance and low latency between instances. Spread is used to isolate instances across separate hardware, while Partition is used for large distributed workloads where instances are separated into failure-isolated partitions."

---

# 25. How to Explain ENI

If asked:

> **"What is an ENI?"**

Say:

> "An Elastic Network Interface is a logical virtual network interface in a VPC. It provides network connectivity to an EC2 instance and can have private IPs, public or Elastic IP associations, and Security Groups. A manually created ENI can also be moved between EC2 instances in the same Availability Zone, which can be useful for network failover."

---

# 26. How to Explain EC2 Hibernate

If asked:

> **"What is EC2 Hibernate?"**

Say:

> "Hibernate allows an EC2 instance to preserve its in-memory state. Before the instance stops, the RAM state is saved to the root EBS volume. When the instance starts again, that state is restored, allowing the operating system and applications to resume instead of starting from a completely fresh boot."

---

# 27. Interview Questions

## Basic

### Q1. What is a private IP?

### Q2. What is a public IP?

### Q3. What is an Elastic IP?

### Q4. What happens to an EC2 public IP after Stop and Start?

### Q5. What is an EC2 Placement Group?

### Q6. What is an ENI?

### Q7. What is EC2 Hibernate?

---

## Intermediate

### Q8. What is the difference between a public IP and an Elastic IP?

### Q9. Why would you use an Elastic IP?

### Q10. What are the three EC2 Placement Group strategies?

### Q11. What is the difference between Cluster and Spread placement groups?

### Q12. What is the difference between Spread and Partition placement groups?

### Q13. Why would you use a Partition Placement Group?

### Q14. Can an ENI be moved between Availability Zones?

### Q15. Why would you create a secondary ENI?

### Q16. What happens to the RAM when an EC2 instance is stopped?

### Q17. What is the difference between Stop and Hibernate?

### Q18. Why must the root EBS volume have enough space when using Hibernate?

---

# 28. Scenario-Based Interview Questions

## Q19. Your EC2 public IP changes every time you stop and start the instance. How would you solve this?

**Answer:**

> "If the application specifically requires a stable public IPv4 address, I could allocate and associate an Elastic IP. However, for production applications I would first consider whether DNS or a Load Balancer is a better architectural solution."

---

## Q20. Two EC2 instances need extremely low-latency, high-throughput communication. Which placement group would you consider?

**Answer:**

> "I would consider a Cluster Placement Group because it places instances close together within an Availability Zone and is designed for high network performance and low latency."

---

## Q21. You have a small number of critical EC2 instances and want to reduce the risk of simultaneous hardware failure. Which placement group?

**Answer:**

> "I would consider a Spread Placement Group because it places instances on separate underlying hardware to reduce correlated hardware failures."

---

## Q22. You are running Kafka with many EC2 instances and want hardware/rack-level failure isolation. Which placement group?

**Answer:**

> "I would consider a Partition Placement Group because it distributes instances across isolated partitions and is suitable for large distributed applications such as Kafka."

---

## Q23. An application has a private IP that must quickly move from one EC2 instance to another during failover. What AWS feature could help?

**Answer:**

> "A manually created ENI can be used because it can carry its private IP and network configuration and be moved between compatible EC2 instances in the same Availability Zone."

---

## Q24. Your application takes several minutes to initialize and you want to preserve its in-memory state between stops. What EC2 feature could you use?

**Answer:**

> "I would consider EC2 Hibernate. It saves the in-memory state to the root EBS volume before stopping and restores it when the instance starts."

---

# 29. Hands-On Scenarios to Practice

## Scenario 1 — Public IP Behavior

Launch an EC2 instance.

Record:

```text
Private IP:
Public IP:
```

Then:

```text
Stop instance
 ↓
Start instance
 ↓
Check IPs again
```

Verify:

```text
Private IP → normally unchanged
Public IP  → may change
```

---

## Scenario 2 — Elastic IP

Practice:

```text
Allocate Elastic IP
        ↓
Associate with EC2
        ↓
Stop EC2
        ↓
Start EC2
        ↓
Check public IP
```

Verify that the Elastic IP remains associated.

When finished:

```text
Disassociate
 ↓
Release Elastic IP
```

This avoids leaving unused resources allocated.

---

## Scenario 3 — Placement Groups

Create three placement groups:

```text
my-cluster-group
→ Cluster

my-spread-group
→ Spread

my-partition-group
→ Partition
```

Launch instances and observe where the placement group is selected during launch.

---

## Scenario 4 — ENI Failover

Create:

```text
EC2-A
EC2-B
```

Then:

```text
Create ENI
 ↓
Attach to EC2-A
 ↓
Record private IP
 ↓
Detach
 ↓
Attach to EC2-B
 ↓
Check private IP
```

Observe that the ENI and its network configuration move to the second instance.

---

## Scenario 5 — EC2 Hibernate

Launch a supported EC2 configuration with:

```text
Hibernation enabled
+
Encrypted root EBS
+
Enough EBS capacity for RAM
```

Then:

```bash
uptime
```

Hibernate the instance:

```text
Running
 ↓
Hibernate
 ↓
Stopped
 ↓
Start
```

Connect again and run:

```bash
uptime
```

Observe that the operating-system uptime reflects the preserved state.

---

# 30. Quick Revision Cheat Sheet

```text
PRIVATE IP
→ Internal/private communication
→ Normally remains after Stop + Start

PUBLIC IP
→ Internet-facing IPv4
→ Can change after Stop + Start

ELASTIC IP
→ Stable public IPv4
→ Allocated to your account
→ Can be associated with a resource
→ Avoid unnecessary use

PLACEMENT GROUPS
→ Control how EC2 instances are placed

CLUSTER
→ Close together
→ High performance / low latency

SPREAD
→ Separate hardware
→ Reduce correlated failures
→ Small number of critical instances

PARTITION
→ Separate partitions/racks
→ Large distributed applications
→ Kafka, Cassandra, Hadoop

ENI
→ Virtual network interface

ENI CAN HAVE
→ Private IPs
→ Public/Elastic IP associations
→ Security Groups
→ MAC address

ENI FAILOVER
→ Move ENI from one EC2 to another
→ Same AZ requirement

HIBERNATE
→ Save RAM state to root EBS
→ Stop instance
→ Restore RAM state on start

HIBERNATE REQUIREMENTS
→ Supported instance/OS
→ Encrypted root EBS
→ Enough EBS capacity for RAM
```

---

# ⭐ 30-Second Section Summary

> **EC2 private IPs are used for private networking, public IPs provide Internet-facing connectivity, and Elastic IPs provide a stable public IPv4 address. Placement Groups control instance placement: Cluster focuses on performance, Spread on hardware failure isolation, and Partition on large distributed workloads. An ENI is a virtual network interface that can carry private IPs, Security Groups, and other network attributes and can be moved between instances in the same Availability Zone. EC2 Hibernate preserves the in-memory state by saving it to the root EBS volume so the instance can resume faster.**

## Golden Memory Trick

```text
IP
├── Private  → Internal
├── Public   → Internet
└── Elastic  → Static public IPv4

Placement Group
├── Cluster   → Performance
├── Spread    → Hardware isolation
└── Partition → Distributed workloads

ENI
→ Virtual network card

Hibernate
→ Save RAM → EBS → Stop → Restore
```
