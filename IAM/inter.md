
# 🧠 AWS IAM Interview Guide for DevOps Engineers

This document covers **core AWS IAM concepts**, **common interview questions**, and **real-world DevOps scenarios**.  
Perfect for brushing up before a DevOps or Cloud Engineer interview!

---

## 🔹 1. Core IAM Concepts Explained

### 🧑‍💻 IAM User
Represents an individual or application that interacts with AWS using **long-term credentials** (username/password or access keys).

- Example: `alice` is a user who can launch EC2 instances.  
- Permissions are attached directly or via groups.

```json
{
  "Effect": "Allow",
  "Action": "ec2:RunInstances",
  "Resource": "*"
}
````

---

### 👥 IAM Groups

A **group** is a collection of IAM users who share the same permissions.
Policies attached to the group apply to all its users.

Example:
Group = `Developers`
Permissions = Read/write to S3, full access to CodeBuild.

---

### 🧩 IAM Roles

A **role** is an identity with **temporary credentials**, assumed by trusted entities (users, services, or accounts).
Roles don’t have long-term credentials.

Example:
An EC2 instance assumes a role with `AmazonS3ReadOnlyAccess` to read from S3.

---

### 🖥️ Instance Profile

An **instance profile** is a container for a role that you attach to an EC2 instance.
It allows the instance to use that role’s permissions securely.

---

### 🔄 Assuming Roles

Assuming a role means temporarily taking on another IAM identity’s permissions using **AWS STS**.

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/DeployRole \
  --role-session-name DevOpsSession
```

---

### 📜 IAM Policies

Policies are **JSON documents** that define what actions are allowed or denied on which resources.

Types:

* **AWS Managed Policies**
* **Customer Managed Policies**
* **Inline Policies**

---

### 🪪 Identity-Based Policies

Attached to IAM **users**, **groups**, or **roles** to define what they can do on which resources.

```json
{
  "Effect": "Allow",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

---

### 📦 Resource-Based Policies

Attached directly to a **resource** (e.g., S3 bucket, Lambda function).
They define **who** can access the resource and **what actions** they can perform.

```json
{
  "Effect": "Allow",
  "Principal": {"AWS": "arn:aws:iam::111122223333:role/CrossAccountRole"},
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

---

### 🧭 Summary Table

| Concept                   | Type      | Purpose                   | Example Use         |
| ------------------------- | --------- | ------------------------- | ------------------- |
| **User**                  | Identity  | Long-term credentials     | Developer login     |
| **Group**                 | Identity  | Manage multiple users     | Developers group    |
| **Role**                  | Identity  | Temporary access          | EC2 accessing S3    |
| **Instance Profile**      | Container | Attach role to EC2        | EC2 S3 read role    |
| **Assume Role**           | Action    | Temporary elevated access | Jenkins deploy role |
| **Policy**                | Document  | Defines permissions       | Allow EC2 actions   |
| **Identity-based Policy** | Policy    | Attached to IAM entities  | User EC2 access     |
| **Resource-based Policy** | Policy    | Attached to AWS resources | S3 bucket access    |

---

## 🔹 2. Top 10 AWS IAM Interview Questions & Answers

### 1. What is AWS IAM?

IAM (Identity and Access Management) enables secure control of access to AWS services and resources using policies, users, roles, and groups.

---

### 2. What are IAM Users, Groups, and Roles?

* **User:** Identity with credentials.
* **Group:** Collection of users with shared permissions.
* **Role:** Temporary identity assumed by entities to access resources.

---

### 3. What are IAM Policies?

JSON documents that define **permissions**.
They specify which **actions** are allowed/denied on which **resources**.

---

### 4. What’s the difference between Inline and Managed Policies?

* **Inline:** Attached directly to one identity.
* **Managed:** Reusable; can be attached to multiple identities (AWS or Customer managed).

---

### 5. How does IAM ensure secure access?

By using:

* Principle of **least privilege**
* **MFA (Multi-Factor Authentication)**
* **Temporary credentials**
* **Granular JSON policies**

---

### 6. What is the Root User and how to secure it?

The **root user** is the original account owner.
Best Practices:

* Enable MFA
* Avoid daily use
* Create admin IAM users instead

---

### 7. Difference between IAM Role and Resource-Based Policy?

* **IAM Role:** Delegates permissions to trusted entities.
* **Resource-Based Policy:** Attached directly to the resource (e.g., S3 bucket).

---

### 8. How can you provide temporary access?

Use **STS (Security Token Service)** with IAM roles to issue **temporary credentials** (short-term, auto-expiring).

---

### 9. How to enforce MFA?

Add a condition in IAM policy:

```json
"Condition": {"BoolIfExists": {"aws:MultiFactorAuthPresent": true}}
```

---

### 10. How to troubleshoot “Access Denied” errors?

* Check IAM user/role policy
* Review resource-based policies
* Test with IAM Policy Simulator
* Verify explicit denies or SCP restrictions

---

## 🔹 3. Scenario-Based IAM Questions (for DevOps)

### 1. Cross-Account Access

**Scenario:** Jenkins in Account A deploys to Account B.
**Solution:** Create a role in Account B, allow Account A to assume it, and use `sts:AssumeRole` for temporary credentials.

---

### 2. Least Privilege Access

**Scenario:** New DevOps engineer needs read-only access.
**Solution:** Create a custom policy with `Describe*`, `Get*`, `List*` actions and attach it to a read-only group.

---

### 3. Temporary Access for External Developers

**Scenario:** Give external developer 2 days of access.
**Solution:** Use STS to issue short-term credentials or SSO session access instead of creating permanent users.

---

### 4. Securing CI/CD Pipelines

**Scenario:** GitHub Actions deploys to AWS.
**Solution:** Use **OIDC federation** and IAM roles to grant temporary access without storing static keys.

---

### 5. Enforcing Organization-Wide Security

**Scenario:** Prevent disabling CloudTrail across multiple accounts.
**Solution:** Use **Service Control Policies (SCPs)** in AWS Organizations to deny `cloudtrail:StopLogging` and `DeleteTrail`.

---

## ✅ Summary

Mastering these IAM concepts and scenarios demonstrates:

* Strong understanding of AWS security best practices
* Ability to design **secure, automated, and scalable** DevOps pipelines
* Real-world readiness for cloud operations and governance

---

**Author:** SALMAN-KHAN
**Updated:** November 2025
**License:** MIT

```

