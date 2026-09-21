# AWS EC2 — Instance Storage — Interview Notes

> **Course:** Section 7 — EC2 Instance Storage  
> **Lectures:** 57–69  
> **Scope:** EBS, EBS Snapshots, AMI, EC2 Instance Store, EBS Volume Types, EBS Multi-Attach, EBS Encryption, Amazon EFS, and EBS vs EFS  
> **Goal:** Easy understanding + hands-on revision + interview preparation

---

# 1. Amazon EBS — Elastic Block Store

**Amazon EBS (Elastic Block Store)** provides persistent **block storage** for EC2 instances.

Think of EBS as a:

> **Network-attached virtual hard disk for EC2.**

```text
EC2 Instance
     |
     | Network
     ↓
EBS Volume
```

## Key characteristics

- EBS is a **network-attached block storage** service.
- Data can persist independently from the EC2 instance.
- An EBS volume is created in a specific **Availability Zone**.
- The volume can be detached and attached to another compatible EC2 instance in the same AZ.
- EBS volumes can be created without immediately attaching them to an instance.
- You provision the volume size and, depending on the volume type, performance characteristics such as IOPS and throughput.
- EBS capacity can be increased when required.
- EBS is commonly used for:
  - OS/root volumes
  - Application data
  - Databases
  - Persistent storage

---

# 2. EBS Availability Zone Limitation

An EBS volume is tied to a specific Availability Zone.

```text
EBS Volume
    |
    ↓
AZ-A
    |
    └── Can attach to EC2 in AZ-A
```

You cannot directly attach:

```text
EBS Volume → AZ-A
       X
EC2 → AZ-B
```

To move EBS data to another AZ:

```text
EBS Volume
     ↓
EBS Snapshot
     ↓
Create Volume
     ↓
Different AZ
```

This is one of the most important EBS concepts.

---

# 3. EBS Can Be Detached and Reattached

Because EBS is network-attached, it can be detached from one EC2 instance and attached to another instance in the same AZ.

Example:

```text
Before:

EC2-A
  |
EBS Volume
```

Detach:

```text
EC2-A

EBS Volume
```

Attach:

```text
EC2-B
  |
EBS Volume
```

This can be useful for:

- Instance replacement
- Failover scenarios
- Data recovery
- Migrating storage between EC2 instances

---

# 4. One EC2 Instance Can Have Multiple EBS Volumes

An EC2 instance can have multiple EBS volumes attached.

```text
             EC2
          /       \
         /         \
      EBS-1       EBS-2
      8 GB        20 GB
```

For example:

```text
EBS-1 → Operating System
EBS-2 → Application Data
EBS-3 → Database Data
```

This allows storage to be separated according to workload requirements.

---

# 5. EBS Delete on Termination

One important EC2/EBS setting is:

> **Delete on Termination**

It controls whether an EBS volume is deleted when its EC2 instance is terminated.

Typical default behavior:

```text
Root EBS Volume
→ Delete on Termination = Enabled

Additional EBS Volume
→ Delete on Termination = Disabled
```

Therefore:

```text
Terminate EC2
      |
      ├── Root EBS → Usually deleted
      |
      └── Additional EBS → Usually preserved
```

You can change this behavior when configuring the instance.

## Interview scenario

**Question:**

> Your EC2 instance contains important data on its root EBS volume. You want to terminate the instance but preserve the volume. What would you check?

**Answer:**

> "I would check the Delete on Termination attribute of the root EBS volume and disable it if I need to preserve the volume after terminating the EC2 instance."

---

# 6. EBS Hands-On

Typical workflow:

```text
EC2 Instance
     ↓
Storage tab
     ↓
Identify EBS Volume
     ↓
Open Volumes
     ↓
Create new EBS Volume
     ↓
Select same Availability Zone
     ↓
Attach to EC2
```

Example:

```text
EC2 → eu-west-1b

EBS Volume → eu-west-1b
      ↓
Attach
      ↓
EC2 now has two EBS volumes
```

If the volume is created in another AZ:

```text
EC2 → eu-west-1b

EBS → eu-west-1a

Attach → Not possible
```

The hands-on demonstrated this AZ restriction directly.

---

# 7. EBS Snapshots

An **EBS Snapshot** is a point-in-time backup of an EBS volume.

```text
EBS Volume
     ↓
Snapshot
     ↓
Backup
```

Snapshots are stored independently of the EBS volume.

## Important points

- Snapshots are used for backup and recovery.
- You can create a snapshot while the volume is attached, although application/database consistency requirements should be considered.
- A snapshot can be used to create a new EBS volume.
- A snapshot can be copied to another Availability Zone by restoring it there.
- Snapshots can also be copied across AWS Regions.
- Snapshots are useful for disaster recovery.

---

# 8. Moving an EBS Volume Across AZs

EBS itself is AZ-specific.

But snapshots allow us to recreate the volume in another AZ.

```text
AZ-A

EBS Volume
     ↓
Snapshot
     ↓
Restore
     ↓
AZ-B

New EBS Volume
```

This is the standard concept to remember:

> **EBS Volume → Snapshot → New EBS Volume in another AZ**

---

# 9. EBS Snapshot and Disaster Recovery

Snapshots can be copied to another AWS Region.

Example:

```text
Region A
   |
EBS Volume
   |
Snapshot
   |
Copy Snapshot
   ↓
Region B
   |
Restore EBS Volume
```

This can be useful for:

- Disaster recovery
- Regional migration
- Backup strategy
- Business continuity

---

# 10. EBS Snapshot Archive

EBS Snapshots can be moved to an **archive tier** for lower-cost long-term storage.

The trade-off is:

```text
Lower storage cost
        +
Longer restore time
```

Archive is therefore better for snapshots that:

- Are rarely accessed
- Need to be retained for a long period
- Do not need immediate restoration

Do not confuse:

```text
EBS Snapshot Archive
→ Storage tier for snapshots
```

with:

```text
EFS Archive
→ Storage tier for EFS files
```

They are different features.

---

# 11. EBS Snapshot Recycle Bin

The **EBS Snapshot Recycle Bin** protects snapshots from accidental deletion.

Instead of immediately losing a deleted snapshot:

```text
Delete Snapshot
      ↓
Recycle Bin
      ↓
Recover if needed
```

You configure retention rules to determine how long deleted snapshots remain recoverable.

The same general Recycle Bin concept can also protect supported resources such as AMIs.

---

# 12. Fast Snapshot Restore

**Fast Snapshot Restore (FSR)** allows an EBS volume created from a snapshot to be fully initialized so applications can avoid the normal initialization-related performance delay.

Useful when:

- The snapshot is large.
- You need to create volumes quickly.
- You need predictable initial performance.

Important:

> **Fast Snapshot Restore provides faster initialization but comes with additional cost.**

---

# 13. AMI — Amazon Machine Image

An **AMI (Amazon Machine Image)** is a template used to launch EC2 instances.

An AMI can contain:

- Operating system
- Installed software
- Application configuration
- System configuration
- Other required setup

Think of an AMI as:

> **A preconfigured template for creating EC2 instances.**

```text
AMI
 |
 +-- OS
 +-- Software
 +-- Configuration
 +-- Application setup
       |
       ↓
   EC2 Instance
```

---

# 14. Why Use a Custom AMI?

Suppose you manually configure an EC2 instance:

```text
Launch EC2
   ↓
Install Apache
   ↓
Install Monitoring Agent
   ↓
Install Application
   ↓
Configure Everything
```

Doing this every time is slow.

Instead:

```text
Configure EC2 once
      ↓
Create Custom AMI
      ↓
Launch new EC2
      ↓
Preconfigured environment
```

This provides:

- Faster instance provisioning
- Consistent configuration
- Reduced manual setup
- Repeatable deployments

---

# 15. AMI Types

You can launch EC2 instances from different types of AMIs.

## AWS-provided AMIs

Examples include AWS-maintained operating system images.

```text
AWS AMI
   ↓
EC2
```

## Custom AMI

Created and maintained by you.

```text
Your EC2
   ↓
Customize
   ↓
Custom AMI
   ↓
Multiple EC2 Instances
```

## AWS Marketplace AMI

AMIs provided by third-party vendors through AWS Marketplace.

They can include:

- Operating systems
- Commercial software
- Security tools
- Application stacks

---

# 16. Creating a Custom AMI

Typical process:

```text
Launch EC2
    ↓
Install/configure software
    ↓
Prepare instance
    ↓
Create AMI
    ↓
AMI created
    ↓
Launch new EC2 instances
```

Creating an AMI involves creating snapshots of the instance's EBS-backed storage behind the scenes.

The course demonstration used:

```text
EC2
 ↓
Install Apache
 ↓
Create AMI
 ↓
Launch new EC2 from AMI
 ↓
Apache already available
```

The second instance therefore required less initialization work.

---

# 17. AMI vs EBS Snapshot

This is an important interview distinction.

```text
EBS Snapshot
→ Backup of an EBS volume

AMI
→ Template used to launch EC2 instances
```

Relationship:

```text
EC2
 ↓
EBS Volumes
 ↓
Snapshots
 ↓
AMI
 ↓
New EC2 Instance
```

An AMI can reference the snapshots required for the instance's storage configuration.

---

# 18. EC2 Instance Store

**EC2 Instance Store** is storage physically attached to the underlying host hardware running the EC2 instance.

Unlike EBS:

```text
EBS
→ Network-attached storage

Instance Store
→ Local storage attached to host
```

---

# 19. Instance Store Performance

Because Instance Store is local to the physical host, it can provide:

- Very high I/O performance
- High throughput
- Very low latency

This makes it useful for workloads requiring extremely fast temporary storage.

Examples:

- Cache
- Temporary data
- Scratch data
- Buffers
- Temporary processing data

---

# 20. Instance Store Is Ephemeral

The biggest disadvantage:

> **Instance Store is ephemeral storage.**

If the EC2 instance is stopped, terminated, or the underlying host fails, the data in Instance Store can be lost.

```text
EC2 Instance
     |
Instance Store
     |
     ↓
Instance/Host failure
     |
     ↓
Data can be lost
```

Therefore:

```text
Long-term persistent data
→ EBS / EFS

Temporary high-performance data
→ Instance Store
```

If you use Instance Store for important data, your application must handle replication/backups appropriately.

---

# 21. EBS vs Instance Store

| Feature | EBS | Instance Store |
|---|---|---|
| Storage type | Network-attached | Local physical storage |
| Persistence | Persistent | Ephemeral |
| Survives instance termination | Can, depending on configuration | No |
| Performance | High | Extremely high for supported workloads |
| Main use | Persistent data | Cache/scratch/temp data |
| Can detach/attach | Yes | No |
| Backup | Snapshots | Application-managed |

### Memory trick

```text
EBS
→ Persistent

Instance Store
→ Temporary + Very Fast
```

---

# 22. EBS Volume Types

The major EBS volume families covered in this section are:

```text
SSD
├── gp2
├── gp3
├── io1
└── io2

HDD
├── st1
└── sc1
```

The main selection factors are:

- Storage size
- IOPS
- Throughput
- Latency
- Cost
- Workload requirements

---

# 23. GP2 — General Purpose SSD

**gp2** is a general-purpose SSD volume.

It is designed for a wide variety of workloads.

Common use cases:

- Boot volumes
- Development/testing
- Virtual desktops
- General applications

Important concept:

> In gp2, performance is tied to volume size.

```text
Larger gp2 volume
        ↓
More IOPS
```

The exact performance limits can vary by AWS configuration, so for current production sizing, always check AWS documentation.

---

# 24. GP3 — General Purpose SSD

**gp3** is the newer generation general-purpose SSD volume.

The key advantage:

> **IOPS and throughput can be configured independently of storage size.**

```text
Storage size
     +
IOPS
     +
Throughput
```

This provides more flexibility than gp2.

### Typical use

Use gp3 when you need:

- General-purpose SSD storage
- Good price/performance
- Control over IOPS
- Control over throughput

### Easy memory

```text
gp2
→ Size affects performance

gp3
→ Size, IOPS and throughput can be configured independently
```

---

# 25. io1 / io2 — Provisioned IOPS SSD

**io1 and io2** are designed for workloads that require high and predictable I/O performance.

Typical workloads:

- High-performance databases
- Mission-critical applications
- Storage-sensitive applications
- Workloads requiring high IOPS

Main concept:

```text
Need predictable high IOPS
        ↓
Provisioned IOPS
        ↓
io1 / io2
```

The exact IOPS, size, and throughput limits depend on the volume type and EC2 instance capabilities.

---

# 26. io2 Block Express

**io2 Block Express** is designed for very demanding storage workloads.

It provides:

- Very high IOPS
- High throughput
- Very low latency
- High durability

Think:

```text
Mission-critical
+
Very high storage performance
        ↓
io2 / io2 Block Express
```

---

# 27. st1 — Throughput Optimized HDD

**st1** is a low-cost HDD volume designed for throughput-intensive workloads.

Typical use cases:

- Big data
- Data warehousing
- Log processing
- Large sequential workloads

Memory:

```text
st1
→ HDD
→ High throughput
→ Frequently accessed throughput-heavy data
```

It is **not intended as a boot volume**.

---

# 28. sc1 — Cold HDD

**sc1** is designed for infrequently accessed data where minimizing storage cost is important.

Typical use cases:

- Infrequently accessed datasets
- Large amounts of cold data
- Cost-sensitive storage

Memory:

```text
sc1
→ HDD
→ Infrequent access
→ Lowest-cost EBS storage class
```

It is also **not intended as a boot volume**.

---

# 29. EBS Volume Types — Easy Comparison

| Volume | Type | Main Purpose |
|---|---|---|
| gp2 | SSD | General purpose |
| gp3 | SSD | General purpose + independent IOPS/throughput |
| io1 | SSD | Provisioned high IOPS |
| io2 | SSD | High-performance, mission-critical workloads |
| st1 | HDD | Throughput-intensive workloads |
| sc1 | HDD | Infrequently accessed data |

### Interview memory

```text
gp3
→ General purpose

io1 / io2
→ High IOPS

st1
→ High throughput HDD

sc1
→ Cold / infrequent HDD
```

---

# 30. How to Choose an EBS Volume

Think about the workload first.

```text
General application
       ↓
gp3

High IOPS database
       ↓
io1 / io2

Large sequential throughput workload
       ↓
st1

Infrequently accessed low-cost data
       ↓
sc1
```

Do not choose an EBS volume only based on storage size.

Consider:

```text
Size
+
IOPS
+
Throughput
+
Latency
+
Cost
+
Workload
```

---

# 31. EBS Multi-Attach

**EBS Multi-Attach** allows a supported EBS volume to be attached to multiple EC2 instances simultaneously.

The feature is available for supported **io1/io2** volumes.

```text
             EC2-A
               |
               |
            EBS Volume
               |
               |
             EC2-B
```

Multiple instances can perform read/write operations on the same volume.

---

# 32. Multi-Attach Availability Zone Limitation

Multi-Attach does **not** remove the EBS Availability Zone restriction.

The instances must be in the same Availability Zone as the EBS volume.

```text
AZ-A

EC2-A
  |
  +---- EBS Multi-Attach
  |
EC2-B
```

Not:

```text
AZ-A                 AZ-B

EC2-A                EC2-B
  \                    /
       EBS Volume
```

---

# 33. Multi-Attach Use Case

Multi-Attach can be useful for certain clustered applications that are designed to coordinate access to shared block storage.

Example:

```text
EC2-A
   \
    \
   Shared EBS
    /
   /
EC2-B
```

The application and filesystem must be designed to safely handle concurrent access.

> **Multi-Attach does not mean any normal filesystem can safely be mounted read/write from multiple EC2 instances.**

This is an important practical point.

---

# 34. EBS Encryption

EBS encryption protects data stored on EBS volumes.

When an EBS volume is encrypted, AWS provides encryption for:

- Data at rest
- Data transferred between the EC2 instance and EBS
- Snapshots created from the encrypted volume
- Volumes created from encrypted snapshots

Encryption is handled transparently by AWS.

```text
EC2
  |
Encrypted communication
  |
EBS
  |
Encrypted data at rest
```

EBS encryption integrates with **AWS KMS**.

---

# 35. Why Use EBS Encryption?

Benefits include:

- Protecting sensitive data
- Meeting security requirements
- Encrypting snapshots
- Encrypting volumes created from encrypted snapshots
- Reducing the need for application-level storage encryption in some scenarios

The performance impact is generally designed to be minimal and AWS manages the encryption/decryption process.

---

# 36. Encrypting an Existing Unencrypted EBS Volume

If you already have an unencrypted EBS volume and want an encrypted version:

```text
Unencrypted EBS Volume
        ↓
Create Snapshot
        ↓
Copy Snapshot
        ↓
Enable Encryption
        ↓
Encrypted Snapshot
        ↓
Create EBS Volume
        ↓
Encrypted EBS Volume
```

Then:

```text
Encrypted EBS
      ↓
Attach to EC2
```

This is an important exam/interview workflow.

---

# 37. Amazon EFS — Elastic File System

**Amazon EFS (Elastic File System)** is a managed **network file system**.

It uses the **NFS protocol** and can be mounted by multiple EC2 instances.

Think of EFS as:

> **A shared network filesystem for multiple Linux-based workloads.**

```text
              EFS
          /     |     \
         /      |      \
      EC2-A   EC2-B   EC2-C
```

Unlike EBS, EFS is designed for shared file access.

---

# 38. EFS and Availability Zones

A Regional EFS filesystem can be accessed by EC2 instances across multiple Availability Zones.

```text
Region
│
├── AZ-A → EC2
│          \
│           \
├── AZ-B → EC2 ---- EFS
│           /
│          /
└── AZ-C → EC2
```

This makes EFS useful when multiple instances need access to the same files across AZs.

---

# 39. EFS Is Scalable

Unlike EBS, you don't provision a fixed volume size in advance.

EFS automatically grows and shrinks based on the data stored.

```text
More files
   ↓
EFS grows

Fewer files
   ↓
Less storage consumed
```

You pay based on the storage and features you use.

This makes EFS useful when storage requirements are difficult to predict.

---

# 40. EFS Use Cases

Common use cases include:

- Shared application files
- Web servers
- Content management systems
- WordPress
- Shared data
- Linux application storage
- Container/shared filesystem workloads

Example:

```text
EC2-A ─┐
       │
EC2-B ─┼── EFS
       │
EC2-C ─┘
```

All instances can access the same filesystem.

---

# 41. EFS Security

EFS uses **Security Groups** to control network access.

EFS uses NFS, so the relevant network traffic is:

```text
NFS
Port 2049
```

Typical architecture:

```text
EC2 Security Group
        ↓
NFS / TCP 2049
        ↓
EFS Security Group
```

The EFS mount targets are associated with security groups.

---

# 42. EFS Mount Targets

To access EFS from a VPC, EFS uses **mount targets** in the Availability Zones where clients need access.

Example:

```text
AZ-A
EC2
 |
Mount Target
 |
EFS

AZ-B
EC2
 |
Mount Target
 |
EFS
```

This allows EC2 instances in different AZs to connect to the same EFS filesystem.

---

# 43. EFS Performance Modes

EFS provides performance modes for different workload requirements.

The important concept is:

```text
General Purpose
→ Lower latency
→ Latency-sensitive applications

Max I/O
→ Higher parallelism
→ Higher latency
→ Suitable for highly parallel workloads
```

For current AWS configurations, always verify which performance modes are available and recommended because AWS evolves the service.

---

# 44. EFS Throughput Modes

The main throughput modes covered are:

```text
Bursting
Elastic
Provisioned
```

## Bursting

Throughput scales with the amount of data stored and can burst when required.

```text
More storage
   ↓
Higher baseline throughput
```

## Provisioned

You specify the throughput you require independently of the amount of data stored.

```text
Storage size
     +
Provisioned throughput
```

Useful when throughput requirements are predictable.

## Elastic

Throughput automatically scales with workload demand.

```text
Workload increases
      ↓
EFS throughput increases

Workload decreases
      ↓
EFS throughput decreases
```

This is useful for workloads with unpredictable I/O requirements.

---

# 45. EFS Storage Classes

EFS provides different storage classes/tiering options.

The main concept is:

```text
Frequently accessed
        ↓
Standard

Infrequently accessed
        ↓
EFS-IA

Rarely accessed
        ↓
Archive
```

## EFS Standard

For frequently accessed files.

## EFS Infrequent Access — EFS-IA

For files that are accessed less frequently.

```text
Lower storage cost
+
Retrieval/access cost
```

## EFS Archive

For rarely accessed data where lower storage cost is important.

---

# 46. EFS Lifecycle Management

Lifecycle management automatically moves files between storage tiers based on access patterns.

Example:

```text
File created
    ↓
EFS Standard
    ↓
Not accessed for a period
    ↓
EFS-IA
    ↓
Not accessed for longer
    ↓
Archive
```

When files become active again, appropriate lifecycle behavior can move them back according to the configured policy.

The important interview concept is:

> **EFS lifecycle management helps reduce storage cost by moving less frequently accessed files to cheaper storage classes.**

---

# 47. Regional EFS vs One Zone EFS

## Regional

Stores data across multiple Availability Zones.

```text
Region
├── AZ-A
├── AZ-B
└── AZ-C
       |
      EFS
```

Advantages:

- Higher availability
- Multi-AZ architecture
- Better suited to production workloads requiring AZ resilience

## One Zone

Stores data within a single Availability Zone.

```text
Region
└── AZ-A
     |
    EFS
```

Advantages:

- Lower storage cost
- Useful for suitable development or cost-sensitive workloads

Trade-off:

> It does not provide the same multi-AZ availability model as Regional EFS.

---

# 48. EFS Encryption

EFS supports encryption at rest using AWS KMS.

```text
EFS
 ↓
Encryption at Rest
 ↓
KMS
```

EFS can therefore protect stored data while still providing normal filesystem access to applications.

---

# 49. EFS Linux Compatibility

The course emphasizes EFS as a Linux-based network filesystem using the standard POSIX filesystem interface.

For the exam/course mental model:

```text
EFS
→ Linux
→ NFS
→ Shared filesystem
```

Do not confuse EFS with EBS:

```text
EFS
→ File storage

EBS
→ Block storage
```

---

# 50. EFS Hands-On

The hands-on demonstrated the complete EFS workflow:

```text
Create EFS
     ↓
Choose VPC
     ↓
Choose Regional EFS
     ↓
Configure lifecycle/performance
     ↓
Configure mount targets
     ↓
Configure Security Groups
     ↓
Create EFS
     ↓
Launch EC2-A
     ↓
Mount EFS
     ↓
Launch EC2-B in another AZ
     ↓
Mount same EFS
     ↓
Create file from EC2-A
     ↓
Read file from EC2-B
```

The hands-on demonstrated that both EC2 instances could access the same EFS filesystem even though they were in different Availability Zones. :contentReference[oaicite:0]{index=0}

---

# 51. EFS Hands-On Example

On the first EC2 instance:

```bash
sudo su

echo "hello world" > /mnt/efs/fs1/hello.txt

cat /mnt/efs/fs1/hello.txt
```

Output:

```text
hello world
```

Then on the second EC2 instance:

```bash
ls /mnt/efs/fs1/

cat /mnt/efs/fs1/hello.txt
```

Output:

```text
hello world
```

This demonstrates:

```text
EC2-A
   |
   | write
   ↓
  EFS
   ↑
   | read
   |
EC2-B
```

The file exists on the shared EFS filesystem, not on only one EC2 instance.

---

# 52. EFS Security Group Hands-On Concept

During the hands-on, AWS created security groups for the EC2/EFS connectivity.

The EFS security group allowed:

```text
NFS
TCP
2049
```

from the appropriate EC2 security group.

Conceptually:

```text
EC2 SG
  |
  | TCP 2049
  ↓
EFS SG
  |
  ↓
EFS
```

This is a very useful real-world networking concept.

---

# 53. EBS vs EFS — Most Important Comparison

This is one of the most important parts of this section.

| Feature | EBS | EFS |
|---|---|---|
| Storage type | Block | File |
| Access model | Block device | Network filesystem |
| Typical attachment | One EC2 at a time | Many EC2 instances |
| Multi-AZ access | Volume is AZ-specific | Regional EFS supports multiple AZs |
| Capacity | Provisioned volume | Automatically scales |
| Protocol | Block storage | NFS |
| Typical OS | Linux/Windows depending on workload | Primarily Linux-based workloads |
| Performance control | Size/IOPS/throughput depending on type | Throughput/performance modes |
| Main use | OS, databases, persistent application data | Shared files |
| Backup | EBS Snapshots | EFS backups/features |
| Cost model | Provisioned capacity/performance | Pay for storage/features used |
| Shared access | Limited/specialized with Multi-Attach | Designed for shared access |

---

# 54. EBS vs EFS — Easy Memory

```text
EBS
→ Block storage
→ Usually one EC2 instance
→ AZ-specific
→ Persistent disk
→ OS / database / application storage

EFS
→ File storage
→ Many EC2 instances
→ Multi-AZ
→ Shared filesystem
→ Web/app/shared files
```

The simplest way to remember:

> **EBS = Hard Disk**

> **EFS = Shared Network Folder**

---

# 55. EBS vs EFS vs Instance Store

Now combine all three:

```text
EBS
→ Persistent block storage

EFS
→ Shared network file storage

Instance Store
→ Temporary local high-performance storage
```

| Requirement | Choose |
|---|---|
| EC2 root disk | EBS |
| Persistent application/database data | EBS |
| Shared files across many EC2 instances | EFS |
| Shared filesystem across AZs | EFS |
| Extremely fast temporary local storage | Instance Store |
| Cache/scratch data | Instance Store |
| Need snapshot-based EBS backup | EBS |
| Need automatic filesystem scaling | EFS |

---

# 56. Common Confusions

## EBS vs EFS

```text
EBS
→ Block storage

EFS
→ File storage
```

---

## EBS vs Instance Store

```text
EBS
→ Persistent

Instance Store
→ Ephemeral
```

---

## EBS Snapshot vs AMI

```text
Snapshot
→ Backup of EBS volume

AMI
→ Template for launching EC2
```

---

## Public IP vs EBS

An EC2 public/private IP has nothing to do with EBS storage.

```text
IP
→ Network connectivity

EBS
→ Storage
```

---

## EBS vs EFS Migration

Do not treat them as interchangeable.

```text
EBS migration
→ Snapshot / restore workflow

EFS migration
→ Filesystem/data migration workflow
```

The storage architecture determines the migration method.

---

## EBS Availability Zone

Remember:

```text
EBS Volume
→ Locked to one AZ
```

To use the data in another AZ:

```text
Snapshot
→ Restore volume in target AZ
```

---

## Multi-Attach

Do not think:

```text
Multi-Attach
→ EBS works across AZs
```

Instead:

```text
Multi-Attach
→ Same supported EBS volume
→ Multiple supported EC2 instances
→ Same AZ
```

---

## EFS vs Multi-Attach

These solve different problems.

```text
EBS Multi-Attach
→ Specialized shared block storage

EFS
→ Designed as a shared network filesystem
```

For normal shared application files, EFS is the more natural storage model.

---

# 57. How to Explain EBS in an Interview

If asked:

> **"What is EBS?"**

Say:

> "Amazon EBS, or Elastic Block Store, is persistent block storage for EC2. It works like a network-attached virtual disk and is associated with a specific Availability Zone. EBS volumes can be detached and attached to another compatible EC2 instance in the same AZ, and snapshots can be used to back up the volume or recreate it in another AZ."

---

# 58. How to Explain EBS Snapshots

If asked:

> **"What is an EBS Snapshot?"**

Say:

> "An EBS Snapshot is a point-in-time backup of an EBS volume. Snapshots can be used to restore new EBS volumes, move data across Availability Zones, and copy backups across Regions for disaster recovery."

---

# 59. How to Explain AMI

If asked:

> **"What is an AMI?"**

Say:

> "An AMI is a template used to launch EC2 instances. It can contain the operating system, installed software, and configuration. A custom AMI allows us to create preconfigured EC2 instances quickly and consistently."

---

# 60. How to Explain Instance Store

If asked:

> **"What is EC2 Instance Store?"**

Say:

> "Instance Store is local storage physically attached to the EC2 host. It can provide very high I/O performance and low latency, but it is ephemeral, so the data can be lost if the instance or underlying host fails. It's therefore suitable for temporary data, cache, and scratch workloads rather than persistent data."

---

# 61. How to Explain EBS Volume Types

If asked:

> **"How do you choose between gp3, io2, st1 and sc1?"**

Say:

> "For general-purpose SSD storage I would typically consider gp3. If the workload requires high and predictable IOPS, such as a storage-sensitive database, I would consider io1 or io2. For throughput-intensive workloads using HDD storage, st1 can be appropriate, while sc1 is intended for infrequently accessed data where low storage cost is the priority."

---

# 62. How to Explain Multi-Attach

If asked:

> **"What is EBS Multi-Attach?"**

Say:

> "EBS Multi-Attach allows a supported io1 or io2 EBS volume to be attached to multiple EC2 instances simultaneously within the same Availability Zone. It is intended for specific clustered applications that are designed to safely coordinate concurrent access to shared block storage."

---

# 63. How to Explain EBS Encryption

If asked:

> **"How do you encrypt an existing unencrypted EBS volume?"**

Say:

> "I can create a snapshot of the unencrypted volume, copy the snapshot with encryption enabled using a KMS key, then create a new encrypted EBS volume from that encrypted snapshot and attach it to the EC2 instance."

---

# 64. How to Explain EFS

If asked:

> **"What is Amazon EFS?"**

Say:

> "Amazon EFS is a managed network file system that uses NFS. Multiple EC2 instances can mount the same EFS filesystem, including instances in different Availability Zones when using Regional EFS. It's useful when applications need shared file storage rather than a single-instance block volume."

---

# 65. How to Explain EBS vs EFS

If asked:

> **"What is the difference between EBS and EFS?"**

Say:

> "EBS is block storage typically attached to an EC2 instance and is associated with a specific Availability Zone. EFS is a managed network filesystem designed for shared access from multiple instances and can provide access across Availability Zones. I would use EBS for things like OS disks and databases, and EFS when multiple instances need to share the same files."

---

# 66. Interview Questions — Basic

### Q1. What is Amazon EBS?

### Q2. What does EBS stand for?

### Q3. What is an EBS volume?

### Q4. Is EBS persistent storage?

### Q5. Is EBS a network-attached storage service?

### Q6. What is an EBS Snapshot?

### Q7. What is an AMI?

### Q8. What is EC2 Instance Store?

### Q9. Is Instance Store persistent?

### Q10. What is Amazon EFS?

### Q11. What protocol does EFS use?

### Q12. What is the main difference between EBS and EFS?

---

# 67. Interview Questions — Intermediate

### Q13. Why can't an EBS volume in AZ-A be directly attached to an EC2 instance in AZ-B?

### Q14. How can you move EBS data from one AZ to another?

### Q15. What is Delete on Termination?

### Q16. What happens to the root EBS volume when an EC2 instance is terminated?

### Q17. What happens to an additional EBS volume when an EC2 instance is terminated?

### Q18. What is the difference between gp2 and gp3?

### Q19. When would you use io1 or io2?

### Q20. What are st1 and sc1 used for?

### Q21. What is EBS Multi-Attach?

### Q22. Which EBS volume families support Multi-Attach?

### Q23. Can Multi-Attach work across Availability Zones?

### Q24. Why is a cluster-aware filesystem important for Multi-Attach?

### Q25. What is EBS encryption?

### Q26. How would you encrypt an existing unencrypted EBS volume?

### Q27. What is the difference between an EBS Snapshot and an AMI?

### Q28. Why would you create a custom AMI?

### Q29. Why is Instance Store faster than EBS in some workloads?

### Q30. Why is Instance Store not suitable for permanent data?

### Q31. What is an EFS mount target?

### Q32. How does a Security Group control access to EFS?

### Q33. What port does NFS commonly use for EFS?

### Q34. What are the EFS throughput modes?

### Q35. What is EFS lifecycle management?

---

# 68. Scenario-Based Interview Questions

## Q36. Your EC2 instance is terminated, but you need to preserve an additional EBS volume. What would you check?

**Answer:**

> "I would check the Delete on Termination attribute of the EBS volume. For an additional data volume, I would normally ensure Delete on Termination is disabled so the volume remains after the EC2 instance is terminated."

---

## Q37. You have an EBS volume in AZ-A and need the same data in AZ-B. What would you do?

**Answer:**

> "I would create an EBS Snapshot of the volume and then create a new EBS volume from that snapshot in the target Availability Zone."

---

## Q38. You need to launch 50 EC2 instances with the same OS, monitoring tools and application dependencies. How could you make deployment faster?

**Answer:**

> "I would create a custom AMI containing the required operating system, software and configuration, and then launch the required EC2 instances from that AMI."

---

## Q39. Your application needs extremely fast temporary storage for cache data. Which EC2 storage option could you consider?

**Answer:**

> "I would consider EC2 Instance Store because it is local storage attached to the EC2 host and can provide very high performance. Since it is ephemeral, I would only use it for data that can be recreated or replicated."

---

## Q40. A database requires predictable high IOPS performance. Which EBS volume family would you consider?

**Answer:**

> "I would consider a provisioned IOPS SSD such as io1 or io2 because they are designed for workloads that require high and predictable storage performance."

---

## Q41. You need a general-purpose SSD for an application and want to configure IOPS and throughput independently from storage size. Which volume would you consider?

**Answer:**

> "I would consider gp3 because it allows storage size, IOPS and throughput to be configured independently."

---

## Q42. Your application generates large amounts of sequential data and requires high throughput at lower cost. Which EBS type could you consider?

**Answer:**

> "I would consider st1 because it is a throughput-optimized HDD volume designed for throughput-intensive workloads such as big data and log processing."

---

## Q43. You have rarely accessed large datasets and want the lowest-cost EBS HDD option. Which volume would you consider?

**Answer:**

> "I would consider sc1 because it is designed for infrequently accessed data where minimizing storage cost is important."

---

## Q44. Several EC2 instances need to access the same files at the same time, including instances in different Availability Zones. What would you consider?

**Answer:**

> "I would consider Amazon EFS because it is a managed network filesystem designed for shared access from multiple EC2 instances and can provide access across Availability Zones with Regional EFS."

---

## Q45. Your EC2 instances are unable to mount EFS. What would you check first?

**Answer:**

> "I would check the network configuration, mount targets, routing, and especially the Security Group rules. EFS uses NFS, so the EC2 instances need appropriate access to the EFS security group on TCP port 2049."

---

## Q46. You need a shared filesystem but do not want to provision storage capacity in advance. What would you consider?

**Answer:**

> "I would consider Amazon EFS because it automatically scales with the amount of data stored and uses a pay-for-usage model."

---

## Q47. Two EC2 instances in different AZs need access to the same persistent files. Would you choose EBS or EFS?

**Answer:**

> "I would generally choose EFS because it is designed as a shared network filesystem and can be accessed by instances across Availability Zones."

---

## Q48. An application requires multiple EC2 instances to access the same block volume concurrently. What AWS feature could support this?

**Answer:**

> "EBS Multi-Attach can support this for supported io1 or io2 volumes, provided the instances are in the same Availability Zone and the application/filesystem is designed for concurrent shared access."

---

# 69. Hands-On Scenarios to Practice

## Scenario 1 — EBS AZ Restriction

Create:

```text
EC2 → AZ-A
EBS → AZ-A
```

Attach the EBS volume.

Then create:

```text
EBS → AZ-B
```

Try to attach it to the EC2 instance in AZ-A.

Observe:

```text
AZ-B EBS
   X
AZ-A EC2
```

---

## Scenario 2 — EBS Snapshot Migration

Practice:

```text
EBS Volume
     ↓
Create Snapshot
     ↓
Create Volume from Snapshot
     ↓
Select different AZ
     ↓
Create
```

Verify that the new volume exists in the target AZ.

---

## Scenario 3 — Delete on Termination

Create an EC2 instance with:

```text
Root EBS
+
Additional EBS
```

Check:

```text
Delete on Termination
```

Then terminate the EC2 instance.

Verify:

```text
Root EBS
→ Deleted if enabled

Additional EBS
→ Preserved if disabled
```

---

## Scenario 4 — Custom AMI

Launch an EC2 instance.

Install/configure something such as Apache.

Then:

```text
EC2
 ↓
Create AMI
 ↓
Launch new EC2 from AMI
```

Verify that the new EC2 instance already contains the software/configuration packaged into the AMI.

---

## Scenario 5 — EBS Volume Types

Create sample volumes and inspect:

```text
gp3
io2
st1
sc1
```

Compare:

```text
Purpose
IOPS
Throughput
Cost
Workload
```

Do not leave unnecessary test volumes running.

---

## Scenario 6 — EBS Encryption

Practice:

```text
Unencrypted EBS
      ↓
Snapshot
      ↓
Copy Snapshot
      ↓
Enable Encryption
      ↓
Encrypted Snapshot
      ↓
Create EBS Volume
```

Verify:

```text
Encryption → Enabled
```

---

## Scenario 7 — EFS Shared Filesystem

Create:

```text
EFS
```

Then:

```text
EC2-A → AZ-A
EC2-B → AZ-B
```

Mount the same EFS filesystem.

From EC2-A:

```bash
echo "hello from EC2-A" > /mnt/efs/fs1/test.txt
```

From EC2-B:

```bash
cat /mnt/efs/fs1/test.txt
```

Expected:

```text
hello from EC2-A
```

This proves that both instances are accessing the same shared filesystem.

---

# 70. Cost Cleanup After Hands-On

Storage resources can continue generating charges.

After practice, clean up:

```text
Terminate EC2 instances
        ↓
Delete unused EBS volumes
        ↓
Delete unnecessary snapshots
        ↓
Delete unused AMIs
        ↓
Delete EFS filesystem
        ↓
Delete unused Security Groups
```

Always check that you are not deleting production or important resources.

---

# 71. Quick Revision Cheat Sheet

```text
EBS
→ Elastic Block Store
→ Persistent block storage
→ Network-attached
→ AZ-specific

EBS SNAPSHOT
→ Point-in-time backup
→ Restore volume
→ Move data across AZs
→ Copy across Regions
→ Disaster recovery

DELETE ON TERMINATION
→ Controls EBS deletion when EC2 terminates

AMI
→ EC2 launch template
→ OS + software + configuration
→ Faster/repeatable EC2 deployment

INSTANCE STORE
→ Local physical storage
→ Very high performance
→ Ephemeral
→ Cache / scratch / temporary data

EBS VOLUME TYPES

gp2
→ General purpose SSD
→ Size affects performance

gp3
→ General purpose SSD
→ IOPS/throughput independently configurable

io1 / io2
→ Provisioned IOPS SSD
→ High-performance workloads

st1
→ Throughput optimized HDD
→ Big data / logs / sequential workloads

sc1
→ Cold HDD
→ Infrequently accessed data
→ Lowest-cost HDD option

MULTI-ATTACH
→ Multiple EC2 instances
→ Supported io1/io2
→ Same AZ
→ Cluster-aware application/filesystem required

EBS ENCRYPTION
→ Encryption at rest
→ Encryption in transit between EC2 and EBS
→ KMS
→ Encrypted snapshots

EFS
→ Elastic File System
→ Managed network filesystem
→ NFS
→ Shared access
→ Multiple EC2 instances
→ Multi-AZ with Regional EFS
→ Automatic scaling

EFS STORAGE
→ Standard
→ IA
→ Archive

EFS THROUGHPUT
→ Bursting
→ Provisioned
→ Elastic

EFS SECURITY
→ Security Groups
→ NFS
→ TCP 2049
```

---

# ⭐ 30-Second Section Summary

> **EBS is persistent block storage for EC2 and is tied to a specific Availability Zone. EBS Snapshots provide point-in-time backups and can be used to recreate volumes in another AZ or Region. AMIs are templates for launching preconfigured EC2 instances. Instance Store is local, very fast but ephemeral storage for temporary workloads. EBS volume types are selected based on workload requirements such as general-purpose performance, high IOPS, throughput, and cost. EBS Multi-Attach allows supported io1/io2 volumes to be attached to multiple EC2 instances in the same AZ. EBS encryption uses AWS KMS to protect data. EFS is a managed NFS-based network filesystem designed for shared access from multiple EC2 instances, including across Availability Zones with Regional EFS.**

---

# 🧠 Golden Memory Trick

```text
STORAGE
│
├── EBS
│   ├── Block storage
│   ├── Persistent
│   ├── AZ-specific
│   └── EC2 disk/database
│
├── EBS Snapshot
│   ├── Backup
│   ├── Restore
│   └── Move across AZ/Region
│
├── AMI
│   └── EC2 template
│
├── Instance Store
│   ├── Local
│   ├── Very fast
│   └── Ephemeral
│
└── EFS
    ├── File storage
    ├── NFS
    ├── Shared
    ├── Multi-AZ
    └── Auto-scaling

EBS VOLUME TYPES
│
├── gp2 → General purpose
├── gp3 → General purpose + independent IOPS/throughput
├── io1 → Provisioned IOPS
├── io2 → High-performance / mission-critical
├── st1 → Throughput HDD
└── sc1 → Cold HDD

EBS MULTI-ATTACH
→ Multiple EC2
→ Same AZ
→ io1/io2
→ Cluster-aware application

EFS
→ Shared filesystem
→ Multiple EC2
→ NFS
→ TCP 2049
```

# 🎯 Most Important Interview Points From This Section

If you have limited revision time, make sure you can confidently explain these:

```text
1. EBS vs EFS
2. EBS vs Instance Store
3. EBS Availability Zone limitation
4. EBS Snapshot and AZ migration
5. Delete on Termination
6. Snapshot vs AMI
7. Custom AMI use case
8. gp2 vs gp3
9. gp3 vs io2
10. st1 vs sc1
11. EBS Multi-Attach
12. EBS Encryption
13. How to encrypt an existing EBS volume
14. EFS shared access
15. EFS Regional vs One Zone
16. EFS Security Group and NFS 2049
17. EFS throughput modes
18. EFS lifecycle/storage tiers
```

> **Core mental model:**  
> **EBS = persistent block disk**  
> **Snapshot = EBS backup**  
> **AMI = EC2 template**  
> **Instance Store = temporary local disk**  
> **EFS = shared network filesystem**
