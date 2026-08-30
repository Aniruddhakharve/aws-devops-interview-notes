# AWS IAM & AWS CLI --- Interview Notes

> **Course:** Section 4 --- IAM & AWS CLI\
> **Scope:** Lectures 11--30 only. Advanced IAM topics are intentionally
> excluded.

------------------------------------------------------------------------

## 1. What is AWS IAM?

**IAM (Identity and Access Management)** is an AWS service used to
securely control **who can access AWS resources and what they are
allowed to do**.

IAM mainly deals with:

-   **Authentication** → Who are you?
-   **Authorization** → What are you allowed to do?

### Simple example

``` text
User
  ↓
IAM Policy
  ↓
Allowed AWS actions
  ↓
AWS Resources
```

------------------------------------------------------------------------

## 2. IAM Users

An **IAM User** is an identity created for a person or application that
needs AWS access.

A user can have:

-   Console access
-   Access keys for programmatic access
-   Permissions through policies

### Example

``` text
Developer
   ↓
IAM User
   ↓
S3 ReadOnly Policy
   ↓
Can read required S3 resources
```

**Important:** Give users only the permissions they actually need.

------------------------------------------------------------------------

## 3. IAM Groups

An **IAM Group** is a collection of IAM users.

Instead of attaching the same policy individually to many users:

``` text
Developer 1 ─┐
Developer 2 ─┼──→ Developers Group
Developer 3 ─┘          ↓
                    S3 Policy
```

This makes permission management easier.

### Example

All developers need read access to S3:

``` text
Developers Group
       ↓
S3 ReadOnly Policy
```

------------------------------------------------------------------------

## 4. IAM Policies

An **IAM Policy** is a JSON document that defines permissions.

It answers:

> **What actions are allowed or denied, and on which resources?**

Example:

``` json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

This allows reading objects from the specified bucket.

### Common policy types covered here

-   **AWS managed policies** → Created and maintained by AWS
-   **Customer managed policies** → Created and maintained by you
-   **Inline policies** → Embedded directly into a single identity

### Least Privilege

Give an identity **only the permissions required to perform its job**.

``` text
Needs S3 read
     ↓
Give S3 read
     ↓
Don't give AdministratorAccess
```

------------------------------------------------------------------------

## 5. IAM Users vs Groups vs Roles

  IAM component   Main purpose
  --------------- -------------------------------------------------------------
  User            Identity for a person/application
  Group           Organize users and share permissions
  Role            Assumable identity, commonly used by AWS services/workloads
  Policy          Defines permissions

### Easy memory trick

``` text
User  → WHO
Group → collection of users
Role  → assumable identity
Policy → WHAT can be done
```

------------------------------------------------------------------------

## 6. IAM MFA

**MFA (Multi-Factor Authentication)** adds another authentication factor
to protect an AWS identity.

Instead of:

``` text
Password
   ↓
Login
```

you use:

``` text
Password
   +
MFA
   ↓
Login
```

MFA helps protect the account even if the password is compromised.

### Best practice

Enable MFA for human users, especially privileged users.

------------------------------------------------------------------------

## 7. AWS Access Keys

Access keys are used for **programmatic access** to AWS.

They consist of credentials such as:

``` text
Access Key ID
Secret Access Key
```

They can be used by:

-   AWS CLI
-   AWS SDKs
-   Applications/scripts

### Important security rule

Never expose access keys in:

-   GitHub repositories
-   Source code
-   Public files
-   Docker images
-   Screenshots

For AWS workloads such as EC2, prefer **IAM Roles** instead of
long-lived access keys when possible.

------------------------------------------------------------------------

# 8. AWS CLI

The **AWS CLI (Command Line Interface)** allows you to interact with AWS
services from a terminal.

Example:

``` bash
aws s3 ls
```

This can list S3 buckets that the identity has permission to see.

### Basic flow

``` text
Terminal
   ↓
AWS CLI
   ↓
AWS Authentication
   ↓
IAM permissions
   ↓
AWS Service
```

------------------------------------------------------------------------

## 9. AWS CLI Credentials

For a user using access keys, credentials can be configured using:

``` bash
aws configure
```

You provide credentials such as:

``` text
AWS Access Key ID
AWS Secret Access Key
Default region
Output format
```

Then AWS CLI can make authenticated requests.

**Remember:** `aws configure` is one way to provide credentials to the
CLI; it is not required when AWS can obtain credentials another way,
such as from an IAM role attached to an EC2 instance.

------------------------------------------------------------------------

# 10. AWS CloudShell

**AWS CloudShell** is a browser-based shell provided by AWS.

It allows you to run AWS CLI commands without manually installing the
AWS CLI on your local computer.

``` text
AWS Console
     ↓
CloudShell
     ↓
AWS CLI commands
     ↓
AWS services
```

It is useful for quickly running AWS CLI commands from the AWS Console.

------------------------------------------------------------------------

# 11. IAM Roles for AWS Services

An **IAM Role** is an identity that can be assumed by a trusted entity.

AWS services such as EC2 can use roles to obtain permissions without
storing long-lived access keys on the server.

### Example: EC2 → S3

``` text
EC2
 ↓
IAM Role
 ↓
S3 Permissions
 ↓
S3 Bucket
```

The EC2 workload can obtain **temporary credentials** for the role.

### Role has two important parts

#### Trust Policy

Defines:

> **Who is allowed to assume this role?**

Example:

``` text
EC2 → allowed to assume → EC2-S3-Role
```

#### Permissions Policy

Defines:

> **What can the role do?**

Example:

``` text
EC2-S3-Role
    ↓
s3:GetObject
```

### Easy memory trick

``` text
Trust Policy       → WHO can assume me?
Permissions Policy → WHAT can I do?
```

------------------------------------------------------------------------

# 12. IAM Role + EC2

When a role is used with EC2, it is associated with the instance through
an **IAM Instance Profile**.

Conceptually:

``` text
IAM Role
   ↓
IAM Instance Profile
   ↓
EC2
```

When launching an EC2 instance, the role can be selected under:

``` text
Advanced details
   ↓
IAM instance profile
```

An existing EC2 instance can also have its IAM role modified.

### Why use a role?

Without a role:

``` text
EC2
 ↓
Long-lived access keys ❌
 ↓
S3
```

With a role:

``` text
EC2
 ↓
IAM Role
 ↓
Temporary credentials
 ↓
S3
```

The second approach is safer and easier to manage.

------------------------------------------------------------------------

# 13. IAM Security Tools

IAM provides tools that help identify security issues and unused
permissions.

### IAM Credentials Report

Provides information about IAM users and their credentials, such as:

-   Password status
-   MFA status
-   Access key information
-   Credential age

### IAM Access Advisor

Shows when a user or role last accessed AWS services.

It can help identify permissions that may no longer be needed.

### Simple distinction

``` text
Credentials Report
→ Information about user credentials/security

Access Advisor
→ Service access/usage information
```

------------------------------------------------------------------------

# 14. IAM Best Practices

Remember these core practices:

1.  **Use least privilege**
2.  **Enable MFA for human users**
3.  **Don't share credentials**
4.  **Don't expose access keys**
5.  **Use IAM Roles for AWS workloads when possible**
6.  **Use groups to manage common user permissions**
7.  **Regularly review unused credentials and permissions**
8.  **Use strong authentication controls**
9.  **Avoid using the root user for everyday tasks**
10. **Give permissions only when required**

------------------------------------------------------------------------

# 15. Hands-On: IAM User

Typical workflow:

``` text
IAM
 ↓
Users
 ↓
Create User
 ↓
Configure required access
 ↓
Attach required permissions
 ↓
Enable MFA
 ↓
Test access
```

------------------------------------------------------------------------

# 16. Hands-On: IAM Policy

Typical workflow:

``` text
IAM
 ↓
Policies
 ↓
Create Policy
 ↓
Choose AWS service
 ↓
Select required actions
 ↓
Restrict resources where possible
 ↓
Create Policy
 ↓
Attach to User / Group / Role
```

------------------------------------------------------------------------

# 17. Hands-On: IAM Role for EC2

Typical workflow:

``` text
IAM
 ↓
Roles
 ↓
Create Role
 ↓
Trusted entity = AWS service
 ↓
Use case = EC2
 ↓
Attach required permissions policy
 ↓
Create Role
 ↓
EC2
 ↓
Advanced details
 ↓
IAM instance profile
 ↓
Select role
```

Then test from EC2:

``` bash
aws s3 ls
```

If the role has the required permissions, the CLI can use the role's
credentials without manually running `aws configure`.

------------------------------------------------------------------------

# 18. Common Confusions

### User vs Role

``` text
Human
 ↓
IAM User / identity
 ↓
Policy

EC2
 ↓
IAM Role
 ↓
Policy
```

### Policy vs Role

``` text
Policy → permissions
Role   → identity that can be assumed
```

### Trust Policy vs Permissions Policy

``` text
Trust Policy
→ Who can assume the role?

Permissions Policy
→ What can the role do?
```

### MFA vs Least Privilege

``` text
MFA
→ Protects authentication

Least Privilege
→ Limits authorization
```

### Access Keys vs IAM Role

``` text
Access Keys
→ Programmatic credentials

IAM Role
→ Preferred for many AWS workloads because it provides temporary credentials
```

------------------------------------------------------------------------

# 19. Interview Questions

## Basic

### Q1. What is IAM?

**Answer:**

> IAM is an AWS service used to manage authentication and authorization.
> It controls who can access AWS resources and what actions they are
> allowed to perform.

### Q2. What is an IAM User?

> An IAM User is an identity that can be given AWS permissions and used
> for console or programmatic access.

### Q3. What is an IAM Group?

> An IAM Group is a collection of IAM users. Policies can be attached to
> the group so common permissions can be managed centrally.

### Q4. What is an IAM Policy?

> An IAM Policy is a JSON document that defines which actions are
> allowed or denied on specific AWS resources.

### Q5. What is least privilege?

> Least privilege means giving an identity only the permissions required
> to perform its job and no unnecessary permissions.

------------------------------------------------------------------------

## Intermediate

### Q6. What is an IAM Role?

> An IAM Role is an assumable identity that provides permissions to
> trusted entities such as AWS services or applications.

### Q7. Why do we use IAM Roles with EC2?

> We use IAM Roles so EC2 applications can access AWS services using
> temporary credentials instead of storing long-lived AWS access keys on
> the server.

### Q8. What is a trust policy?

> A trust policy defines which principal is allowed to assume an IAM
> role.

### Q9. What is the difference between a trust policy and a permissions policy?

> A trust policy defines who can assume the role, while a permissions
> policy defines what actions the role is allowed to perform.

### Q10. What is MFA?

> MFA adds an additional authentication factor, such as an authenticator
> code or security key, in addition to the password.

------------------------------------------------------------------------

## Scenario-Based

### Q11. EC2 needs to read files from S3. How would you provide access?

**Good interview answer:**

> I would create an IAM role trusted by EC2 and attach a policy that
> grants only the required S3 read permissions. Then I would attach the
> role to the EC2 instance through its IAM instance profile. This avoids
> storing long-lived AWS access keys on the server.

### Q12. A developer only needs to read one S3 bucket. What would you do?

> I would follow least privilege and create or use a policy that allows
> only the required S3 read actions on the specific bucket rather than
> giving broad S3 or administrator permissions.

### Q13. Multiple developers need the same AWS permissions. What would you use?

> I would place the users in an IAM Group and attach the required policy
> to the group, rather than managing the same permissions individually
> for every user.

### Q14. An EC2 instance is running `aws s3 ls` without `aws configure`. How is this possible?

> The EC2 instance can have an IAM role attached through an IAM instance
> profile. AWS provides temporary credentials for that role, which the
> AWS CLI can use automatically.

------------------------------------------------------------------------

# 20. How to Explain IAM in an Interview

If the interviewer says:

> **"Explain IAM."**

A strong concise answer is:

> "IAM stands for Identity and Access Management. It is used to control
> authentication and authorization in AWS. We use IAM Users or other
> identities for human access, Groups to manage common user permissions,
> Policies to define permissions, and Roles for trusted workloads such
> as EC2. We follow least privilege by granting only the permissions
> required, and we use MFA to strengthen authentication for human
> users."

### If they ask about EC2 specifically

> "For EC2, I would normally use an IAM Role instead of storing access
> keys on the instance. The role has a trust policy allowing EC2 to
> assume it and a permissions policy defining what the instance can
> access. The role is associated with EC2 through an IAM instance
> profile, allowing the workload to obtain temporary credentials."

------------------------------------------------------------------------

# 21. Quick Revision Cheat Sheet

``` text
IAM
→ Manage AWS authentication and authorization

USER
→ Human/application identity

GROUP
→ Collection of users

POLICY
→ Defines permissions

ROLE
→ Assumable identity

TRUST POLICY
→ Who can assume the role?

PERMISSIONS POLICY
→ What can the role do?

MFA
→ Additional authentication factor

LEAST PRIVILEGE
→ Minimum required permissions

ACCESS KEYS
→ Programmatic credentials

AWS CLI
→ Manage AWS through terminal

CLOUDSHELL
→ Browser-based AWS shell

INSTANCE PROFILE
→ Associates an IAM role with EC2

EC2 → ROLE → POLICY → AWS SERVICE
```

------------------------------------------------------------------------

# 22. Hands-On Scenarios to Practice

Try these **without looking at the notes**.

### Scenario 1 --- S3 Read Access

Create an IAM user that can only read objects from an S3 bucket.

Ask yourself:

-   What policy do I need?
-   Which S3 actions?
-   Can I restrict the resource to one bucket?
-   Can the user perform an operation that wasn't allowed?

------------------------------------------------------------------------

### Scenario 2 --- Developer Group

Create:

``` text
Developers Group
   ↓
S3 ReadOnly Policy
   ↓
2 IAM Users
```

Verify that both users receive the group's permissions.

------------------------------------------------------------------------

### Scenario 3 --- EC2 → S3

Create an IAM role for EC2 with the required S3 permissions.

Then:

``` text
Role
 ↓
EC2
 ↓
aws s3 ls
```

Verify that the command works **without running `aws configure` on the
EC2 instance**.

------------------------------------------------------------------------

### Scenario 4 --- Least Privilege

Create a policy that allows:

``` text
s3:GetObject
```

for only one bucket.

Then test:

``` text
Allowed → Read object from permitted bucket
Denied  → Perform unauthorized action
Denied  → Access unrelated resource
```

------------------------------------------------------------------------

### Scenario 5 --- Security Review

Use IAM security tools to investigate:

``` text
Which users have MFA?
Which credentials exist?
Which AWS services have been accessed?
Which permissions/credentials may be unused?
```

------------------------------------------------------------------------

# ⭐ 30-Second IAM Revision

If you have only 30 seconds before an interview, remember:

``` text
IAM
│
├── User       → identity
├── Group      → collection of users
├── Policy     → permissions
├── Role       → assumable identity
├── MFA        → stronger authentication
└── Least Privilege → minimum permissions

Human
  ↓
Identity/User/Group
  ↓
Policy

AWS Workload
  ↓
Role
  ↓
Policy

EC2
  ↓
Instance Profile
  ↓
IAM Role
  ↓
Temporary Credentials
  ↓
AWS Services
```

**Golden interview line:**

> **"Policies define what an identity can do; roles provide an assumable
> identity for trusted workloads; and least privilege means granting
> only the permissions actually required."**
