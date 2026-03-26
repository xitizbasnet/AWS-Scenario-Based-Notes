# ☁️ AWS Scenario-Based Interview Guide

> **Purpose:** A professional reference guide for IT practitioners and cloud engineers preparing for scenario-based AWS interviews.
> **Audience:** Cloud Engineers, DevOps Engineers, Solutions Architects, IT Support Staff
> **Format:** Scenario → Diagnosis → Solution → Prevention

---

## 📋 Table of Contents

1. [EC2 Instance Suddenly Not Reachable](#1-ec2-instance-suddenly-not-reachable)
2. [Website is Slow Only at Night](#2-website-is-slow-only-at-night)
3. [S3 Data Accidentally Deleted](#3-s3-data-accidentally-deleted)
4. [RDS Database Running but Application Cannot Connect](#4-rds-database-running-but-application-cannot-connect)
5. [High AWS Bill Suddenly](#5-high-aws-bill-suddenly)
6. [Application Down in One Region](#6-application-down-in-one-region)
7. [IAM Access Denied](#7-iam-access-denied)
8. [EC2 CPU Always at 100%](#8-ec2-cpu-always-at-100)
9. [How to Secure an S3 Bucket](#9-how-to-secure-an-s3-bucket)
10. [CloudFormation Deployment Failed](#10-cloudformation-deployment-failed)
11. [NAT Gateway vs Internet Gateway](#11-nat-gateway-vs-internet-gateway)
12. [Share Data Between EC2 Instances](#12-share-data-between-ec2-instances)
13. [Low Latency Global Application](#13-low-latency-global-application)
14. [Handle Secrets Securely](#14-handle-secrets-securely)
15. [Load Balancer Health Check Failing](#15-load-balancer-health-check-failing)
16. [Pro Tip: The AWS Troubleshooting Framework](#-pro-tip-the-aws-troubleshooting-framework)

---

## 1. 🖥️ EC2 Instance Suddenly Not Reachable

### 📌 Scenario
Your EC2 instance was working fine, but you can suddenly no longer connect via SSH.

### 🔍 Diagnostic Checklist

Perform the following checks in order, using a **layered troubleshooting approach**:

| Layer | Check | Details |
|-------|-------|---------|
| 🔐 Network | **Security Group** | Is port 22 open? |
| 🔐 Network | **Network ACL (NACL)** | Is it blocking inbound/outbound traffic? |
| 💻 Instance | **Instance State** | Is the instance in a `Running` state? |
| 🌐 Network | **Public IP** | Has the IP changed? (Occurs if Elastic IP is not assigned) |
| 🖥️ OS | **Resource Usage** | Is CPU usage high or disk full? |

### ✅ Resolution Approach

```
Network Layer → Instance Layer → OS Layer
```

> 💬 **Key Interview Line:**
> *"I follow a layered approach: Network → Instance → OS level troubleshooting."*

---

## 2. 🐢 Website is Slow Only at Night

### 📌 Scenario
Your application performs well during the day but becomes noticeably slow at night.

### 🔍 Likely Causes

- Unexpected traffic spike occurring at night
- Insufficient compute resources during peak off-hours load

### 🛠️ Diagnostic Steps

1. Open **Amazon CloudWatch**
2. Review the following metrics:
   - CPU Utilization
   - Memory Usage
   - Network I/O

### ✅ Recommended Solutions

| Solution | Service |
|----------|---------|
| Automatic scaling | **Auto Scaling Group** |
| Traffic distribution | **Elastic Load Balancer (ELB)** |
| Caching | **Amazon CloudFront** or **Redis (ElastiCache)** |

> 💬 **Key Interview Line:**
> *"I would confirm the root cause using CloudWatch metrics before making any scaling decisions."*

---

## 3. 🗑️ S3 Data Accidentally Deleted

### 📌 Scenario
A team member has accidentally deleted important files from an S3 bucket.

### 🔍 Recovery Options

| Situation | Recovery Method |
|-----------|----------------|
| ✅ Versioning was enabled | Restore from a previous version |
| ❌ Versioning was not enabled | Check AWS Backup or Amazon S3 Glacier |

### 🛡️ Prevention Measures

- ✅ Enable **S3 Versioning** on all critical buckets
- ✅ Enable **MFA Delete** to prevent accidental or unauthorized deletions

> 💬 **Key Interview Line:**
> *"Always enable versioning for critical buckets — it's the first line of defence against accidental deletion."*

---

## 4. 🗄️ RDS Database Running but Application Cannot Connect

### 📌 Scenario
The RDS instance is in a running state, but the application is unable to establish a connection.

### 🔍 Diagnostic Checklist

- [ ] **Security Group** — Is the correct inbound port (e.g., 3306 for MySQL) open?
- [ ] **DB Endpoint** — Is the application using the correct endpoint URL?
- [ ] **Credentials** — Are the username and password correct?
- [ ] **Public/Private Access** — Is the instance configured for public or private access as expected?
- [ ] **Subnet Group** — Is the RDS instance in the correct VPC subnet group?

> 💬 **Key Interview Line:**
> *"In most cases, RDS connectivity issues stem from network or Security Group misconfigurations."*

---

## 5. 💸 High AWS Bill Suddenly

### 📌 Scenario
Your AWS bill has increased significantly and unexpectedly.

### 🔍 Common Cost Culprits

- Unused or over-provisioned **EC2 instances**
- Unallocated **Elastic IP addresses** (charged when not associated)
- High **data transfer** costs
- Excessive **NAT Gateway** usage

### ✅ Recommended Actions

| Action | Tool / Service |
|--------|---------------|
| Monitor and alert on spend | **AWS Billing Alerts** (via CloudWatch) |
| Analyse cost breakdown | **AWS Cost Explorer** |
| Identify idle resources | **AWS Trusted Advisor** |

> 💬 **Key Interview Line:**
> *"NAT Gateway is a hidden cost that many teams overlook — always monitor its usage."*

---

## 6. 🌍 Application Down in One Region

### 📌 Scenario
Your application is unavailable in one AWS region, impacting end users in that area.

### ✅ Recommended Architecture for High Availability

- Deploy a **Multi-Region** architecture
- Configure **Amazon Route 53** with a failover routing policy
- Maintain a **backup deployment** in a secondary region

> 💬 **Key Interview Line:**
> *"True high availability requires region-level redundancy — a single region is never sufficient for critical workloads."*

---

## 7. 🔐 IAM Access Denied

### 📌 Scenario
A user or service is receiving an `Access Denied` error when attempting to access an AWS resource.

### 🔍 Areas to Investigate

| Type | Description |
|------|-------------|
| **IAM Policy** | Check the user/role's attached identity-based policies |
| **Service Control Policy (SCP)** | Check for organisation-level restrictions (AWS Organizations) |
| **Resource Policy** | Check the resource's own policy (e.g., S3 bucket policy, KMS key policy) |

### ⚠️ Key IAM Rule

> An **explicit `Deny`** in any policy always overrides an `Allow` — regardless of where it is defined.

> 💬 **Key Interview Line:**
> *"Explicit deny always wins — check all three policy layers before concluding."*

---

## 8. ⚡ EC2 CPU Always at 100%

### 📌 Scenario
An EC2 instance is consistently running at 100% CPU utilisation.

### 🔍 Investigation Steps

1. Connect to the instance and check running processes (`top`, `htop`)
2. Identify the process consuming the most CPU
3. Determine if the issue is application-level or infrastructure-level

### ✅ Solutions

| Approach | Method |
|----------|--------|
| Immediate fix | **Vertical scaling** (upgrade instance type) |
| Long-term solution | Configure an **Auto Scaling Group** |

> 💬 **Key Interview Line:**
> *"Auto Scaling is preferred over vertical scaling for cost efficiency and resilience."*

---

## 9. 🔒 How to Secure an S3 Bucket

### 📌 Scenario
You need to ensure that an S3 bucket is fully secured against unauthorised access.

### ✅ Security Measures

| Measure | Description |
|---------|-------------|
| 🚫 **Block Public Access** | Enable at the bucket and account level |
| 👤 **IAM Policies** | Restrict access using least-privilege principles |
| 🔐 **Encryption** | Enable SSE-S3, SSE-KMS, or client-side encryption |
| 📋 **Bucket Policies** | Apply resource-based policies for fine-grained access control |
| 🗂️ **Access Logging** | Enable S3 server access logging for audit trails |

> 💬 **Key Interview Line:**
> *"The first and most impactful step is enabling Block Public Access — it prevents any accidental public exposure."*

---

## 10. 🛠️ CloudFormation Deployment Failed

### 📌 Scenario
A CloudFormation stack deployment has failed and rolled back.

### 🔍 How to Diagnose

1. Navigate to the **CloudFormation Console**
2. Select the failed stack
3. Open the **Events** tab
4. Identify the first resource with a `CREATE_FAILED` or `UPDATE_FAILED` status
5. Read the status reason for the exact error

### ✅ Resolution Steps

1. Fix the identified issue in the CloudFormation template
2. Re-deploy the corrected template
3. Validate the template using `aws cloudformation validate-template` before redeployment

> 💬 **Key Interview Line:**
> *"The Events tab gives you the exact root cause — always start there before reviewing the template."*

---

## 11. 🌐 NAT Gateway vs Internet Gateway

### 📌 Scenario
You need to decide whether to use a NAT Gateway or an Internet Gateway for your VPC architecture.

### 🔍 Comparison

| Feature | Internet Gateway (IGW) | NAT Gateway |
|---------|------------------------|-------------|
| **Direction** | Inbound & Outbound | Outbound only |
| **Used by** | Public subnets | Private subnets |
| **Assigns Public IP** | Yes | No |
| **Typical Use Case** | Web servers, public-facing apps | Private instances downloading updates |

> 💬 **Key Interview Line:**
> *"Private instances use NAT Gateway for outbound internet access — they should never route through the IGW directly."*

---

## 12. 🗂️ Share Data Between EC2 Instances

### 📌 Scenario
Multiple EC2 instances need to access and share the same data or file system.

### ✅ Recommended Solutions

| Solution | Best For |
|----------|----------|
| **Amazon EFS (Elastic File System)** | Shared POSIX-compliant file system, mounted concurrently by multiple EC2 instances |
| **Amazon S3** | Object storage for shared data, accessible via SDK or CLI |

> 💬 **Key Interview Line:**
> *"EFS is the best option when you need a shared file system — it can be mounted simultaneously by multiple EC2 instances."*

---

## 13. 🌏 Low Latency Global Application

### 📌 Scenario
Your application serves users globally and requires low-latency content delivery.

### ✅ Recommended Solution

Use **Amazon CloudFront** (Content Delivery Network / CDN):

- Distributes content from **over 400 edge locations** worldwide
- Serves requests from the **nearest edge location** to the user
- Integrates with **S3, EC2, ALB, and custom origins**
- Supports **HTTPS, WAF, and caching policies**

> 💬 **Key Interview Line:**
> *"CloudFront serves content from the nearest edge location, dramatically reducing latency for global users."*

---

## 14. 🔑 Handle Secrets Securely

### 📌 Scenario
Your application requires database credentials, API keys, or other sensitive values. How do you handle them securely?

### ✅ Recommended Approach

| Tool | Use Case |
|------|----------|
| **AWS Secrets Manager** | Credentials requiring automatic rotation (e.g., DB passwords) |
| **AWS Systems Manager Parameter Store** | Configuration values and non-rotating secrets |

### ❌ What to Avoid

- **Never hardcode** secrets in source code or environment variables
- **Never commit** credentials to version control (e.g., GitHub)

> 💬 **Key Interview Line:**
> *"AWS Secrets Manager supports automatic secret rotation — it removes the need for manual credential management."*

---

## 15. ❤️ Load Balancer Health Check Failing

### 📌 Scenario
The Load Balancer is reporting that targets are unhealthy and health checks are failing.

### 🔍 Diagnostic Checklist

- [ ] **Health check path** — Is the configured path correct and returning HTTP 200?
- [ ] **Application status** — Is the web server or application running on the target instances?
- [ ] **Security Group rules** — Does the Security Group allow traffic from the Load Balancer on the health check port?
- [ ] **Port configuration** — Is the correct port specified in the target group settings?

> 💬 **Key Interview Line:**
> *"Health check misconfiguration is one of the most common causes — always verify the path and port first."*

---

## 💡 Pro Tip: The AWS Troubleshooting Framework

For any AWS issue, apply the following structured approach:

```
🔍 Understand  →  🩺 Diagnose  →  🛠️ Solve  →  🛡️ Prevent
```

| Phase | Action |
|-------|--------|
| 🔍 **Understand** | Clarify the symptoms, scope, and impact |
| 🩺 **Diagnose** | Use CloudWatch, CloudTrail, and AWS Console to identify the root cause |
| 🛠️ **Solve** | Apply the appropriate fix at the correct layer |
| 🛡️ **Prevent** | Implement best practices, alerts, and automation to prevent recurrence |

---

## 📚 Additional Resources

| Resource | Link |
|----------|------|
| AWS Documentation | [docs.aws.amazon.com](https://docs.aws.amazon.com) |
| AWS Well-Architected Framework | [aws.amazon.com/architecture/well-architected](https://aws.amazon.com/architecture/well-architected/) |
| AWS CloudWatch | [aws.amazon.com/cloudwatch](https://aws.amazon.com/cloudwatch/) |
| AWS Cost Explorer | [aws.amazon.com/aws-cost-management/aws-cost-explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/) |
| AWS IAM Best Practices | [IAM Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) |

---
