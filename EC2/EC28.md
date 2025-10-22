# AWS Load Balancer and EC2 Setup Guide

This guide provides step-by-step instructions for setting up:

* Classical Load Balancer (CLB)
* Application Load Balancer (ALB)
* Network Load Balancer (NLB)
* EC2 instances with Load Balancers
* Auto Scaling Groups (ASG)
* Launch Templates
* Sticky Sessions
* Path-based Routing
* Getting Client IP on ALB
* Termination Protection
* Hibernate vs Power Off
* Reserved Instances
* Attaching Multiple Network Interfaces (NICs)

---

## 1. Classical Load Balancer (CLB)

### ✅ Purpose:

Make your EC2 instances accessible **only through** the Load Balancer.

### 📌 Steps:

1. **Launch EC2 Instances**

   * Choose Amazon Linux or Ubuntu.
   * Disable public IP (optional if you want private-only access).
   * Make sure web server (e.g., Apache) is running.

2. **Security Groups**

   * EC2 SG: Allow HTTP **only from CLB**'s SG.
   * CLB SG: Allow inbound HTTP from anywhere (`0.0.0.0/0`).

3. **Create Classic Load Balancer**

   * Go to EC2 → Load Balancers → Create → **Classic Load Balancer**.
   * Select listeners (HTTP:80).
   * Add EC2 instances.
   * Configure health checks.

4. **Test**

   * Access via CLB DNS name.
   * Direct access to EC2 IP should be blocked.

---

## 2. Application Load Balancer (ALB) – Layer 7

### ✅ Features:

* Path-based routing
* Host-based routing
* Stickiness
* Client IP logging

---

### 📌 Setup Steps:

1. **Launch EC2 Instances** (App 1, App 2)

   * Install and run web servers serving different apps (e.g., `/app1`, `/app2`).

2. **Create Target Groups**

   * Target Group 1: `/app1`
   * Target Group 2: `/app2`
   * Register EC2s accordingly.

3. **Create Application Load Balancer**

   * Select **Application Load Balancer**
   * Scheme: Internet-facing
   * Listeners: HTTP (80)
   * Add security group (allow HTTP)

4. **Configure Path-Based Routing**

   * Rule 1: `/app1/*` → Target Group 1
   * Rule 2: `/app2/*` → Target Group 2

---

### 💡 Getting Client IP Address on ALB

ALB adds the real IP in the `X-Forwarded-For` header.

In your app or web server, check this header:

* Apache: Use `mod_remoteip`
* NGINX:

  ```nginx
  real_ip_header X-Forwarded-For;
  ```

---

### 🔁 Enable Stickiness (Session Affinity)

* Go to Target Group → Attributes → Enable Stickiness
* Choose duration (e.g., 300 seconds)

---

### 🎨 Custom Error/Page Routing

Add ALB rule:

* If path `/404` → return fixed response or redirect

---

## 3. Network Load Balancer (NLB) – Layer 4

### ✅ Features:

* TCP traffic
* Low latency
* Preserves client IP

### 📌 Steps:

1. **Create NLB**

   * Scheme: Internet-facing
   * Listener: TCP (e.g., 80)

2. **Target Group**

   * Protocol: TCP
   * Register EC2s

3. **Security Group**

   * Attach to EC2 (NLB does not have a security group)

4. **Test with IP-based Applications**

---

## 4. Launch Template & Auto Scaling Group (ASG)

### 📌 Launch Template

1. EC2 → Launch Templates → Create Template

   * AMI, instance type, key pair
   * User data (for bootstrapping)
   * Security groups

### 📌 Auto Scaling Group

1. Go to EC2 → Auto Scaling Groups → Create
2. Choose Launch Template
3. Attach to existing Load Balancer (ALB/NLB)
4. Set desired, min, and max instance count
5. Add scaling policy (CPU threshold)

---

## 5. Termination Protection

* Go to EC2 instance → Actions → Instance Settings → **Enable Termination Protection**
* Prevents accidental deletion (must be disabled before termination)

---

## 6. Hibernate vs Stop/Power Off

| Action                 | Preserves RAM | Charges for RAM | Fast Resume |
| ---------------------- | ------------- | --------------- | ----------- |
| Hibernate              | ✅ Yes         | ✅ Yes           | ✅ Yes       |
| Stop                   | ❌ No          | ❌ No            | ❌ No        |
| Power Off (force stop) | ❌ No          | ❌ No            | ❌ No        |

---

## 7. Reserved Instances (RI)

### 📌 Steps:

1. Go to EC2 → Reserved Instances → Purchase Reserved Instances
2. Choose:

   * Instance type
   * Region
   * Term (1 or 3 years)
   * Payment option (All upfront, Partial, No upfront)

✅ Cost savings for steady workloads.

---

## 8. Multiple NICs (Elastic Network Interfaces)

### 📌 Steps:

1. Go to EC2 → Network Interfaces → Create
2. Attach to a subnet
3. Assign security group
4. Attach to EC2 Instance (must be in same AZ)
5. Configure OS to use additional interface (e.g., `eth1`)

---

## ✅ Summary Diagram

```txt
Internet
   │
 ┌───────────┐
 │  Load     │
 │ Balancer  │──────────────┐
 └───────────┘              │
   │                        │
   ▼                        ▼
[Target Group]         [Target Group]
   │                        │
   ▼                        ▼
[EC2: App1]             [EC2: App2]
```

---

